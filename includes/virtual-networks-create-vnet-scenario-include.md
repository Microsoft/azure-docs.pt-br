## <a name="scenario"></a>Cenário
Para ilustrar melhor como se cria uma Rede Virtual e sub-redes, este documento usará o cenário a seguir.

![Cenário de Rede Virtual](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Neste cenário, você criará uma Rede Virtual denominada **TestVNet** com um bloco CIDR reservado de **192.168.0.0./16**. A Rede Virtual conterá as seguintes sub-redes: 

* **FrontEnd**, usando **192.168.1.0/24** como seu bloco CIDR.
* **BackEnd**, usando **192.168.2.0/24** como seu bloco CIDR.

