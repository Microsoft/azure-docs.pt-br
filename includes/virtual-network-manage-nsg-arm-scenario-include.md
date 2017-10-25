## <a name="sample-scenario"></a>Cenário de exemplo
Para melhor ilustrar como gerenciar NSGs, este artigo usará o cenário abaixo.

![Cenário de Rede Virtual](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Nesse cenário, você criará um NSG para cada sub-rede na rede virtual **TestVNet** , conforme descrito abaixo: 

* **NSG-FrontEnd**. O front-end de NSG será aplicado à sub-rede *FrontEnd* contém duas regras:    
  * **rdp-rule**. Essa regra permitirá tráfego RDP para a sub-rede *FrontEnd* .
  * **web-rule**. Essa regra permitirá tráfego HTTP para a sub-rede *FrontEnd* .
* **NSG-BackEnd**. O frontend de NSG será aplicado à sub-rede *BackEnd* e contém duas regras:    
  * **sql-rule**. Essa regra permite o tráfego SQL somente da sub-rede *FrontEnd* .
  * **web-rule**. Esta regra nega todo o tráfego limitado da Internet da sub-rede *BackEnd* .

A combinação dessas regras cria um cenário de DMZ, em que a sub-rede de back-end só pode receber tráfego de entrada para tráfego SQL da sub-rede front-end e não tem acesso à Internet, enquanto a sub-rede de front-end pode se comunicar com a Internet e receber somente solicitações HTTP de entrada.

Para implantar o cenário descrito acima, acesse [este link](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal. Nas instruções de exemplo abaixo, o modelo foi usado para implantar nomes do grupo de recursos **RG-NSG**. 

