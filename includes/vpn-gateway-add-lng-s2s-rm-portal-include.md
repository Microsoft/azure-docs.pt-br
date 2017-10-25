1. No portal, de **Todos os recursos**, clique em **+ Adicionar**. 
2. Na caixa de pesquisa da folha **Tudo**, digite **Gateway de rede local** e clique em pesquisar. Isso retornará uma lista. Clique em **Gateway de rede local** para abrir a folha, clique em **Criar** para abrir a falha **Criar gateway de rede local**.

  ![criar gateway de rede local](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Na **folha Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

  - **Nome:** especifique um nome para seu objeto de gateway de rede local.
  - **Endereço IP:** este é o endereço IP público do dispositivo VPN ao qual você deseja que o Azure se conecte. Especifique um endereço IP público válido. O endereço IP não pode estar por trás do NAT e deve poder ser acessado pelo Azure. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados na captura de tela, mas você precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
  - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Use seus próprios valores aqui, não os valores mostrados na captura de tela*.
  - **Assinatura:** verifique se a assinatura correta está sendo exibida.
  - **Grupo de Recursos:** selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
  - **Local:** selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

4. Quando tiver terminado de especificar os valores, clique em **Criar** na parte inferior da folha de criar o gateway de rede local.