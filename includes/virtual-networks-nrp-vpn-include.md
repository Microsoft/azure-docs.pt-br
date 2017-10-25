## <a name="vpn-gateway"></a>Gateway de VPN
Um recurso de gateway de VPN permite que você crie uma conexão segura entre seu data center local e o Azure. Um recurso de gateway de VPN pode ser configurado de três maneiras diferentes:

* **Ponto a Site** – você pode acessar com segurança os recursos do Azure hospedados em uma VNET com um cliente VPN, em qualquer computador. 
* **Conexão multissite** – você pode conectar-se com segurança por meio de seus data centers locais para recursos em execução em uma VNET. 
* **VNET para VNET** – você pode conectar-se com segurança entre VNETS do Azure na mesma região ou em regiões diferentes, para criar cargas de trabalho com redundância geográfica.

Propriedades principais de um gateway de VPN incluem:

* **Tipo de gateway** - gateway roteado dinamicamente ou estaticamente. 
* **Prefixo do Pool de endereços do cliente de VPN** – endereços IP a serem atribuídos a clientes conectando-se em configuração de ponto a site.

