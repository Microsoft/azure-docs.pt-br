As SKUs herdadas (antigas) do gateway de VPN são:

* Basic
* Standard
* HighPerformance

O gateway de VPN não usa a SKU do gateway UltraPerformance. Para saber mais sobre a SKU do UltraPerformance, veja a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Ao trabalhar com os SKUs herdados, considere o seguinte:

* Se quiser usar um tipo PolicyBased VPN, você deverá usar a SKU Básica. VPNs PolicyBased (anteriormente chamadas de Roteamento Estático) não têm suporte em qualquer outro tipo de SKU.
* BGP não tem suporte na SKU Básica.
* Configurações de coexistência de ExpressRoute-Gateway de VPN não têm suporte na SKU Básica.
* As conexões de Gateway de VPN S2S ativa-ativa só podem ser configuradas na SKU HighPerformance.