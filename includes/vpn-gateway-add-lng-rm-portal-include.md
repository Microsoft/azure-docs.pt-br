1. No portal, de **Todos os recursos**, clique em **+ Adicionar**. Na caixa de pesquisa da folha **Tudo**, digite **Gateway de rede local** e clique para retornar uma lista de recursos. Clique em **Gateway de rede local** para abrir a folha, clique em **Criar** para abrir a falha **Criar gateway de rede local**.
   
    ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)

2. Na **folha Criar gateway de rede local**, especifique um **Nome** para seu objeto de gateway de rede local. Se possível, use algo intuitivo, como **ClassicVNetLocal** ou **TestVNet1Local**. Isso facilita identificar o gateway de rede local no portal.
3. Especifique um **endereço IP** público válido para o dispositivo VPN ou um gateway de rede virtual ao qual você deseja conectar-se.<br>**Se essa rede local representar um caminho local:** especifique o endereço IP público do dispositivo VPN ao qual você deseja conectar-se. Ele não pode estar por trás do NAT e deve poder ser acessado pelo Azure.<br>**Se essa rede local representar outra VNet:** especifique o endereço IP público atribuído ao gateway de rede virtual para essa VNet.<br>**Se você ainda não tiver o endereço IP:** poderá criar um endereço IP de espaço reservado válido, em seguida, voltar e modificar essa configuração antes de se conectar.
4. **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos especificados aqui não se sobrepõem aos intervalos de outras redes com as quais você deseja conectar-se.
5. Para **Assinatura**, verifique se a assinatura correta está sendo exibida.
6. Para **Grupo de Recursos**, selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
7. Em **Local**, selecione o local no qual esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.
8. Clique em **Criar** para criar o gateway de rede local.

