## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar uma rede virtual clássica no portal do Azure
Para criar uma rede virtual clássica com base no cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **NOVO** > **Rede** > **Rede virtual**, observe que a lista **Selecionar um modelo de implantação** já mostra **Clássico** e, em seguida, clique em **Criar**, conforme visto na figura abaixo.
   
    ![Criar rede virtual no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. Na folha **Rede virtual**, digite o **Nome** da rede virtual e clique em **Espaço de endereço**. Defina as configurações de espaço de endereço para a rede virtual e sua primeira sub-rede e clique em **OK**. A figura abaixo mostra as configurações do bloco CIDR do nosso cenário.
   
    ![Folha Espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. Clique em **Grupo de Recursos** e selecione um grupo de recursos ao qual adicionar a rede virtual, ou clique em **Criar novo grupo de recursos** para adicionar a rede virtual a um novo grupo de recursos. A figura a seguir mostra as configurações de um novo grupo de recursos denominado **TestRG**. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Folha Criar grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. Se necessário, altere as configurações de **Assinatura** e **Local** da sua rede virtual. 
6. Se não deseja ver a rede virtual como um bloco no **Quadro Inicial**, desabilite **Fixar no Quadro Inicial**. 
7. Clique em **Criar** e observe o bloco chamado **Criando a rede virtual**, conforme mostrado na figura abaixo.
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. Aguarde até que a rede virtual seja criada, e quando visualizar o bloco abaixo, clique nele para adicionar mais sub-redes.
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. Você deverá ver a **Configuração** para sua rede virtual como mostrada abaixo. 
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. Clique em **Sub-redes** > **Adicionar**. Em seguida, digite um **Nome** e especifique um **Intervalo de endereços (bloco CIDR)** para a sub-rede e clique em **OK**. A figura abaixo mostra as configurações do nosso cenário atual.
    
    ![Criar rede virtual no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

