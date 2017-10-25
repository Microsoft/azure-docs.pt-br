### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar o 'gatewayIpAddress' para um gateway de rede local

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. O endereço IP do gateway pode ser alterado sem remover uma conexão de gateway de VPN existente (se houver uma). Para modificar o endereço IP do gateway, substitua os valores 'Site2' e 'TestRG1' pelos seus próprios valores usando o comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verifique se o endereço IP está correto na saída:

```
"gatewayIpAddress": "23.99.222.170",
```