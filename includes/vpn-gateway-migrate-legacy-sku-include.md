> [!NOTE]
> * O endereço IP público do gateway de VPN será alterado com a migração de uma SKU antiga para uma nova.
> * Você não pode migrar gateways de VPN clássicos para novos SKUs. Gateways de VPN clássicos só podem usar os SKUs (antigos) herdados.
> 

Você não pode redimensionar seus gateways de VPN do Azure entre as SKUs antigas e as novas famílias de SKU. Se você tiver gateways VPN no modelo de implantação do Gerenciador de Recursos que estão usando a versão mais antiga de SKUs, poderá migrar para as novas SKUs. Para migrar, exclua o gateway de VPN existente para sua rede virtual e crie um novo.

Fluxo de trabalho de migração:

1. Remova todas as conexões com o gateway de rede virtual.
2. Exclua o gateway de VPN antigo.
3. Crie o novo gateway de VPN.
4. Atualize os dispositivos VPN locaia com o novo endereço IP do gateway de VPN (para conexões Site a Site).
5. Atualize o valor de endereço IP do gateway para gateways de rede local VNet para VNet que se conectam a esse gateway.
6. Baixe novos pacotes de configuração do cliente VPN para clientes de P2S que se conectam à rede virtual por meio desse gateway de VPN.
7. Recrie as conexões para o gateway de rede virtual.