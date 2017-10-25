## <a name="scenario"></a>Cenário
Para ilustrar melhor como criar UDRs, este documento usará o cenário abaixo.

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Neste cenário, você criará um UDR para a *Sub-rede de front-end* e outro UDR para a *Sub-rede de back-end*, como descrito abaixo: 

* **UDR-FrontEnd**. O UDR de front-end será aplicado à sub-rede *FrontEnd* e contém uma rota:    
  * **RouteToBackend**. Essa rota enviará todo o tráfego à sub-rede de back-end para a máquina virtual **FW1** .
* **UDR-BackEnd**. O UDR de back-end será aplicado à sub-rede *BackEnd* e contém uma rota:    
  * **RouteToFrontend**. Essa rota enviará todo o tráfego à sub-rede de front-end para a máquina virtual **FW1** .

A combinação dessas rotas garantirá que todo o tráfego destinado de uma sub-rede a outra será roteado para a máquina virtual **FW1** , que está sendo usada como um dispositivo virtual. Você também precisa ativar o encaminhamento IP para essa VM para garantir que ela possa receber o tráfego destinado a outras VMs.

