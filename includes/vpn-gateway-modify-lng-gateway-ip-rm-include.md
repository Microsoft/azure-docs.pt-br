### <a name="gwipnoconnection"></a> Para modificar o gateway de rede local 'gatewayIpAddress' - sem conexão de gateway

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. Use o exemplo para modificar um gateway de rede local que não tenha uma conexão de gateway.

Ao modificar esse valor, você também pode modificar os prefixos do endereço ao mesmo tempo. Use o nome existente do seu gateway de rede local para poder sobrescrever as configurações atuais. Se você usar um nome diferente, um novo gateway de rede local será criado, em vez de substituir o existente.

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a> Para modificar o gateway de rede local 'gatewayIpAddress' - conexão de gateway existente

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. Se uma conexão de gateway já existir, primeiro você precisa remover a conexão. Após a conexão ser removida, você pode modificar o endereço IP do gateway e recriar uma nova conexão. Você também pode modificar os prefixos do endereço ao mesmo tempo. Isso resulta em algum tempo de inatividade para a conexão VPN. Ao modificar o endereço IP de gateway, você não precisa excluir o gateway de VPN. Você precisa apenas remover a conexão.
 

1. Remova a conexão. Você pode encontrar o nome da sua conexão usando o cmdlet ‘Get-AzureRmVirtualNetworkGatewayConnection’.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Modifique o valor de ‘GatewayIpAddress’. Você também pode modificar os prefixos do endereço ao mesmo tempo. Use o nome existente do seu gateway de rede local para sobrescrever as configurações atuais. Se você não fizer isso, um novo gateway de rede local será criado, em vez de substituir o existente.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Crie a conexão. Neste exemplo, configuramos um tipo de conexão IPsec. Quando você recriar a conexão, use o tipo de conexão especificado para sua configuração. Para outros tipos de conexão, consulte a página [Cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obter o nome do VirtualNetworkGateway, você pode executar o cmdlet 'Get-AzureRmVirtualNetworkGateway'.
   
    Defina as variáveis.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Crie a conexão.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```