1. No portal, no lado esquerdo, clique em **+** e digite "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** na pesquisa, retorne e clique na entrada. Na página **Gateway da rede virtual**, clique em **Criar** na parte inferior da página para abrir a página **Criar gateway da rede virtual**.
2. Na página **Criar gateway de rede virtual**, preencha os valores para seu gateway de rede virtual.

  ![Criar campos na página do gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Criar campos na página do gateway de rede virtual")
3. **Nome**: nomeie o seu gateway. Nomear um gateway não é igual a nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
4. **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**.
5. **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige um tipo de VPN baseado em rota.
6. **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado.
7. **Local**: ajuste o campo **Local** para apontar para o local onde está sua rede virtual. Se o local não estiver apontando para a região onde reside sua rede virtual, a rede virtual não aparecerá na lista suspensa 'Escolher uma rede virtual'.
8. Escolha a rede virtual à qual você deseja adicionar um gateway. Clique em **Rede virtual** para abrir a página **Escolher uma rede virtual**. Selecione a rede virtual. Se você não vir a sua rede virtual, verifique se o campo **Local** está apontando para a região na qual sua rede virtual está localizada.
9. **Endereço IP público**: crie um objeto de endereço IP público para o qual um endereço IP público será atribuído dinamicamente. Clique em **Endereço IP público** para abrir a página **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir a **página Criar endereço IP público**. Dê um nome para o seu endereço IP público. Clique em **OK** para salvar as alterações. O endereço IP é atribuído dinamicamente quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. No entanto, isso não significa que o endereço IP mudará depois de ter sido atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.
10. **Assinatura**: verifique se a assinatura correta foi selecionada.
11. **Grupo de recursos**: essa configuração é determinada pela Rede Virtual selecionada.
12. Não ajuste o **Local** depois de especificar as configurações acima.
13. Verifique as configurações. Se você quiser que seu gateway apareça no painel, poderá selecionar **Fixar no painel** na parte inferior da página.
14. Clique em **Criar** para começar a criar o gateway. As configurações são validadas e o gateway é implantado. A criação de um gateway pode levar até 45 minutos.

Depois de criar o gateway, você pode exibir o endereço IP atribuído a ele observando a rede virtual. O gateway aparecerá como um dispositivo conectado. Você pode clicar no dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.