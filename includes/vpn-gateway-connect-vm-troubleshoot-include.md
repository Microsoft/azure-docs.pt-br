Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

- Verifique se a conexão VPN é estabelecida.
- Verifique se você está se conectando ao endereço IP privado para a VM.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Quando você se conectar via Ponto a Site, verifique os seguintes itens adicionais:

- Use 'ipconfig' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP está dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso é chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local.
- Verifique se o pacote de configuração de cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a rede virtual. Se você atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre como solucionar problemas de conexões RDP, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).