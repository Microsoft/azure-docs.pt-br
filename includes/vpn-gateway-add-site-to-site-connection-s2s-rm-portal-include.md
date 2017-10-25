1. Navegue até a folha do seu gateway de rede virtual e a abra. Há várias maneiras de navegar. Em nosso exemplo, podemos navegar para o gateway 'VNet1GW' acessando **TestVNet1 -> Visão geral -> Dispositivos conectados -> VNet1GW**.
2. Na folha do VNet1GW, clique em **Conexões**. Na parte superior da folha Conexões, clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.

    ![Criar uma conexão site a site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Na folha **Adicionar conexão**, preencha os valores para criar a conexão.

  - **Nome:** nomeie sua conexão. Nós usamos **VNet1toSite2** em nosso exemplo.
  - **Tipo de conexão:** selecione **Site a site (IPSec)**.
  - **Gateway de rede virtual:** o valor é fixo porque você está se conectando por esse gateway.
  - **Gateway de rede local:** clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que você deseja usar. Em nosso exemplo, nós usamos **Site2**.
  - **Chave Compartilhada:** o valor aqui deve corresponder ao valor usado para seu dispositivo VPN local. Neste exemplo, usamos ‘abc123’, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
  - Os valores restantes para **Assinatura**, **Grupo de Recursos** e **Local** são corrigidos.

4. Clique em **OK** para criar sua conexão. Você verá *Criar Conexão* piscar na tela.
5. Você pode exibir a conexão na folha **Conexões** do seu gateway de rede virtual. O Status será alterado de *Desconhecido* para *Conectando* e então para *Êxito*.