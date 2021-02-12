---
title: 'Conectar-se a uma VNet de uma VPN de computador-P2S e autenticação de certificado nativa do Azure: PowerShell'
description: Conecte clientes Windows e macOS com segurança à rede virtual do Azure usando P2S e certificados autoassinados ou emitidos por AC. Este artigo usa o PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660399"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configurar uma conexão VPN ponto a site para uma VNet usando a autenticação de certificado nativa do Azure: PowerShell

Este artigo ajuda você a conectar com segurança clientes individuais que executam Windows, Linux ou macOS a uma VNet do Azure. As conexões VPN ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como quando você está intercomutando de casa ou de uma conferência. Também é possível usar P2S em vez de uma VPN Site a Site, quando você tiver apenas alguns clientes que precisam se conectar a uma VNet. As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. A P2S cria a conexão VPN no SSTP (Secure Socket Tunneling Protocol) ou IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Conectar-se de um computador a um diagrama de conexão de ponto a site da VNet do Azure":::

Para obter mais informações sobre VPN ponto a site, consulte [sobre VPN ponto a site](point-to-site-about.md). Para criar essa configuração usando o portal do Azure, consulte [Configurar uma VPN ponto a site usando o portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Muitas das etapas neste artigo podem usar o Azure Cloud Shell. No entanto, você não pode usar Cloud Shell para gerar certificados. Além disso, para carregar a chave pública do certificado raiz, você deve usar Azure PowerShell localmente ou a portal do Azure.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Entrar

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. declarar variáveis

Usamos variáveis para este artigo para que você possa alterar facilmente os valores a serem aplicados ao seu próprio ambiente sem precisar alterar os exemplos. Declare as variáveis que você quer usar. Você pode usar o exemplo a seguir, substituindo os valores por seu próprio quando necessário. Se você fechar sua sessão do PowerShell/Cloud Shell a qualquer momento durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. configurar uma VNet

1. Crie um grupos de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Crie as configurações de sub-rede para a rede virtual, nomeando-as como *front-end* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada por você.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Crie a rede virtual.

   Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando a partir da sua rede virtual. Este exemplo um endereço IP privado, mas é provável que ele não seja o endereço IP do seu servidor DNS. Use seus próprios valores. O valor especificado é usado pelos recursos que são implantados para a rede virtual, não com a conexão de P2S no cliente de VPN.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Especifique as variáveis da rede virtual que você criou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Um gateway de VPN deve ter um endereço IP público. Você primeiro solicita o recurso de endereço IP e, em seguida, faz referência a ele ao criar seu gateway de rede virtual. O endereço IP é atribuído dinamicamente ao recurso quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. Você não pode solicitar uma atribuição de endereço IP Público Estático. No entanto, isso não significa que o endereço IP mudará depois de ter sido atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

   Solicite um endereço IP público atribuído dinamicamente.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. criar o gateway de VPN

Nesta etapa, você configura e cria o gateway de rede virtual para sua VNet.

* O -GatewayType deve ser **Vpn** e o -VpnType deve ser **RouteBased**.
* O -VpnClientProtocols é usado para especificar os tipos de túneis que você deseja habilitar. As opções de túnel são **OpenVPN, SSTP** e **IKEv2**. Você pode optar por habilitar uma delas ou qualquer combinação com suporte. Se você quiser habilitar vários tipos, especifique os nomes separados por uma vírgula. OpenVPN e SSTP não podem ser habilitados juntos. O cliente strongSwan no Android e no Linux, e o cliente VPN IKEv2 nativo no iOS e no OSX usarão somente o túnel IKEv2 para se conectar. Os clientes Windows tentam o IKEv2 primeiro e, se isso gerar a conexão, retornarão ao SSTP. Você pode usar o cliente OpenVPN para se conectar ao tipo de túnel OpenVPN.
* O SKU ' básico ' do gateway de rede virtual não oferece suporte à autenticação IKEv2, OpenVPN ou RADIUS. Se você estiver planejando clientes Mac, se conecte à sua rede virtual, não use a SKU básica.
* Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do [SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md) selecionado. Este exemplo usa IKEv2.

1. Configurar e criar o gateway de rede virtual para sua rede virtual. Leva aproximadamente 45 minutos para que o gateway seja criado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Depois que o gateway for criado, você poderá exibi-lo usando o exemplo a seguir. Se você fechou o PowerShell ou atingiu o tempo limite enquanto o gateway estava sendo criado, você pode [declarar suas variáveis](#declare) novamente.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. Adicionar o pool de endereços do cliente VPN

Depois que o gateway de VPN é criado, você pode adicionar o pool de endereços do cliente VPN. O pool de endereços de cliente VPN é o intervalo do qual os clientes VPN recebem um endereço IP ao se conectar. Use um intervalo de endereço IP privado que não coincida com o local de onde você se conecta ou com a rede virtual à qual você deseja se conectar.

Neste exemplo, o pool de endereços do cliente VPN é declarado como uma [variável](#declare) em uma etapa anterior.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. gerar certificados

>[!IMPORTANT]
> Você não pode gerar certificados usando Azure Cloud Shell. Você deve usar um dos métodos descritos nesta seção. Se você quiser usar o PowerShell, deverá instalá-lo localmente.
>

Os certificados são usados pelo Azure para autenticar clientes VPN para VPNs ponto a site. Você pode carregar as informações da chave públicas do certificado raiz no Azure. A chave pública é considerada “trusted” (confiável). Os certificados de cliente devem ser gerados a partir do certificado raiz confiável e, em seguida, em cada computador cliente no repositório de certificados de usuário/pessoal de certificados atual. O certificado é usado para autenticar o cliente quando ele inicia uma conexão de rede virtual. 

Se você usa certificados autoassinados, eles devem ser criados usando parâmetros específicos. Você pode criar um certificado autoassinado usando as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou, se não tiver o Windows 10, com o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que você siga as etapas nas instruções ao gerar os certificados raiz autoassinados e os certificados de cliente. Caso contrário, os certificados gerados não serão compatíveis com conexões P2S e você receberá um erro de conexão.

### <a name="root-certificate"></a><a name="cer"></a>Certificado raiz

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Depois de criar o certificado raiz, [exporte](vpn-gateway-certificates-point-to-site.md#cer) os dados do certificado público (não a chave privada) como um arquivo X. 509. cer codificado na base64.

### <a name="client-certificate"></a><a name="generate"></a>Certificado do cliente

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Depois de criar o certificado do cliente, [exporte](vpn-gateway-certificates-point-to-site.md#clientexport) -o. O certificado do cliente será distribuído para os computadores cliente que serão conectados.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. carregar as informações de chave pública do certificado raiz

Verifique se a criação do gateway de VPN foi concluída. Depois de concluído, você pode carregar o arquivo. cer (que contém as informações de chave pública) para um certificado raiz confiável do Azure. Uma vez carregado o arquivo .cer, o Azure pode usá-lo para autenticar clientes com um certificado de cliente instalado gerado a partir de um certificado raiz confiável. Você pode carregar arquivos de certificado raiz confiável adicionais - até um total de 20 - posteriormente, se necessário.

>[!NOTE]
> Não é possível carregar o arquivo. cer usando Azure Cloud Shell. Você pode usar o PowerShell localmente em seu computador ou pode usar as etapas de [portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Declare a variável para o nome do certificado, substituindo o valor pelo seu.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Substitua o caminho do arquivo pelo seu e execute os cmdlets.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Carregue as informações de chave pública para o Azure. Depois que as informações do certificado são carregadas, o Azure o considera um certificado raiz confiável. Ao carregar, verifique se você está executando o PowerShell localmente no seu computador ou, em vez disso, você pode usar as [etapas de portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Não é possível carregar usando Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. instalar um certificado de cliente exportado

As etapas a seguir o ajudarão a instalar o em um cliente do Windows. Para clientes adicionais e mais informações, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Verifique se o certificado do cliente foi exportado como um .pfx juntamente com a cadeia de certificado inteira (que é o padrão). Caso contrário, as informações do certificado raiz não estão presentes no computador cliente e este não será capaz de fazer a autenticação corretamente.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. configurar o cliente VPN

Nesta seção, você configura o Native Client para seu computador se conectar ao gateway de rede virtual. Por exemplo, ao acessar as configurações de VPN no computador com Windows, você pode adicionar conexões VPN. Uma conexão ponto a site requer definições de configuração específicas. Essas etapas ajudam a criar um pacote com as configurações específicas que seu cliente VPN nativo precisa para poder se conectar à rede virtual por meio de uma conexão ponto a site.

Você pode usar os exemplos rápidos a seguir para gerar e instalar o pacote de configuração do cliente. Para obter mais informações sobre o conteúdo do pacote e instruções adicionais sobre como gerar e instalar arquivos de configuração de cliente VPN, consulte [criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).

Se você precisar declarar suas variáveis novamente, poderá encontrá-las [aqui](#declare).

### <a name="to-generate-configuration-files"></a>Para gerar arquivos de configuração

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Para instalar o pacote de configuração do cliente

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. conectar-se ao Azure

### <a name="windows-vpn-client"></a>Cliente VPN do Windows

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Cliente VPN Mac

Na caixa de diálogo de Rede, localize o perfil de cliente que você deseja usar e, em seguida, clique em **Conectar**.
Verifique [ Instalar - Mac (OSX)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) para obter instruções detalhadas. Se você estiver tendo problemas para se conectar, verifique se que o gateway de rede virtual não está usando uma SKU básica. Não há suporte para a SKU básica para clientes Mac.

  ![Conexão Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Para verificar uma conexão

Essas instruções se aplicam a clientes do Windows.

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu é um dos endereços dentro do pool de endereços do cliente VPN ponto a site que você especificou em sua configuração. Os resultados são semelhantes a este exemplo:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Para conectar-se a uma máquina virtual

Essas instruções se aplicam a clientes do Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Verifique se o pacote de configuração de cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a rede virtual. Se você atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

* Use 'ipconfig' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP está dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso é chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Para adicionar ou remover um certificado raiz

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado raiz, os clientes que possuem um certificado gerado pelo certificado raiz não podem fazer a autenticação e, portanto, não conseguem se conectar. Se você deseja que um clientes faça autenticação e se conecte, você precisa instalar um novo certificado de cliente gerado a partir de um certificado confiável (carregado) no Azure. Essas etapas exigem Azure PowerShell cmdlets instalados localmente no seu computador (não Azure Cloud Shell). Você também pode usar o portal do Azure para adicionar certificados raiz.

**Para adicionar:**

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. As etapas a seguir ajudam a adicionar um certificado raiz. 

1. Prepare o arquivo .cer para upload:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Carregue o arquivo. Você só pode carregar um arquivo por vez.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Para verificar se o arquivo de certificado foi carregado:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Para remover:**

1. Declare as variáveis. Modifique as variáveis no exemplo para corresponder ao certificado que você deseja remover.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Remova o certificado.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Use o exemplo a seguir para verificar se o certificado foi removido com êxito.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Para revogar ou restabelecer um certificado de cliente

É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente a conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticação.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

**Para revogar:**

1. Recupere a impressão digital do certificado de cliente. Para obter mais informações, consulte [como recuperar a impressão digital de um certificado](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).

1. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua. A cadeia de caracteres é declarada como uma variável na próxima etapa.

1. Declare as variáveis. Declare a impressão digital recuperada na etapa anterior.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Adicione a impressão digital à lista de certificados revogados. Você verá "Êxito" quando a impressão digital tiver sido adicionada.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Verifique se a impressão digital foi adicionada à lista de certificados revogados.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Após a adição da impressão digital, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado recebem uma mensagem informando que o certificado não é mais válido.

**Para restabelecer:**

Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1. Declare as variáveis. Declare a impressão digital correta para o certificado que você deseja restabelecer.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Remova a impressão digital do certificado da lista de revogação de certificado.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Verifique se a impressão digital foi removida da lista revogada.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Perguntas frequentes sobre Ponto a site

Para obter informações adicionais de ponto a site, consulte [FAQ de ponto a site do gateway de VPN](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](../index.yml). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/network-overview.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).