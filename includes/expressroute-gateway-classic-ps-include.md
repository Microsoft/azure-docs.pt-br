Você deve criar uma rede virtual e uma sub-rede de gateway primeiro, antes de iniciar as tarefas a seguir. Confira o artigo [Configurar uma Rede Virtual usando o portal clássico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obter mais informações.   

## <a name="add-a-gateway"></a>Adicionar um gateway
Use o comando a seguir para criar um gateway. Certifique-se de substituir quaisquer valores pelos seus próprios.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Verificar se o gateway foi criado
Use o comando abaixo para verificar se o gateway foi criado. Esse comando também recupera a ID do gateway, que você precisa para outras operações.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Há uma série de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

> [!IMPORTANT]
> Esse comando não funciona para o gateway UltraPerformance. Para alterar o gateway para um gateway UltraPerformance, primeiro remova o gateway do ExpressRoute existente e crie um novo gateway UltraPerformance. Para fazer downgrade do gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e crie um novo gateway. 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway
Use o comando a seguir para remover um gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>