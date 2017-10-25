Você pode criar máquinas virtuais no Azure usando o Gerenciador de Servidores no Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Criar uma máquina virtual do Azure no Gerenciador de Servidores
Embora você possa criar uma máquina virtual no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=253103), também pode criar uma máquina virtual no Azure usando comandos no Gerenciador de Servidores. Máquinas virtuais podem ser usadas, por exemplo, para fornecer um front-end por trás de um ponto de extremidade público com balanceamento de carga comum.

### <a name="to-create-a-new-virtual-machine"></a>Criar uma nova máquina virtual
1. No Gerenciador de Servidores, abra o nó **Azure** e clique em **Máquinas Virtuais**.
2. No menu de contexto, clique em **Criar Máquina Virtual**.
   
    O assistente **Criar uma Nova Máquina Virtual** será exibido.
   
    ![O comando Criar máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. Na página **Escolher uma Assinatura**, selecione uma assinatura a ser usada ao criar uma máquina virtual e clique em **Avançar**.
   
    Se você não tiver entrado no Azure, clique em **Entrar** para entrar. Em seguida, selecione sua assinatura do Azure na caixa de listagem suspensa, se ela ainda não estiver selecionada.
4. Na página **Selecionar uma Imagem de Máquina Virtual**, selecione um tipo de imagem na caixa de listagem suspensa **Tipo de imagem** e selecione imagens de máquina virtual na caixa de listagem **Nome da imagem**. Quando terminar, clique em **Próximo**.
   
    ![Selecionar uma página de imagem de máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Você pode escolher os seguintes tipos de imagem:
   
   * **Imagens públicas** lista imagens de máquinas virtuais de sistemas operacionais e software para servidores, como o Windows Server e SQL Server.
   * **Imagens MSDN** lista imagens de máquinas virtuais do software disponível para assinantes do MSDN, como o Visual Studio e o Microsoft Dynamics.
   * **Imagens privadas** lista imagens especializadas e generalizadas da máquina virtual que você criou.
     
     Para saber mais sobre máquinas virtuais especializadas e generalizadas, consulte [Imagem da VM](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Consulte [Como capturar uma máquina Virtual do Windows para usar como modelo](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) para obter informações sobre como transformar uma máquina virtual em um modelo que você pode usar para criar rapidamente novas máquinas virtuais pré-configuradas.
     
     Para obter informações sobre a imagem, você pode clicar no nome de imagem de máquina virtual no lado direito da página.
     
     > [!NOTE]
     > Não é possível adicionar imagens de máquinas virtuais às listas **Imagens Públicas** ou **Imagens MSDN** porque elas são somente leitura. Todas as máquinas virtuais que você cria são adicionadas na lista **Imagens privadas** .
     > 
     > 
     
     Se for assinante do MSDN com uma assinatura do nível do Visual Studio, você poderá criar uma máquina virtual do Azure pré-criada que contém o Visual Studio, além de várias outras imagens. Para saber mais, confira [Create a Virtual Machine in Visual Studio by Using Images Visual Studio 2013 Gallery image for MSDN subscribers (Criar uma máquina virtual no Visual Studio usando imagens da galeria de imagens do Visual Studio 2013 para assinantes do MSDN)](http://visualstudio2013msdngalleryimage.azurewebsites.net) e [Assinaturas do MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs).|
5. Na página **Configurações básicas de máquina Virtual** , insira um nome de máquina e, em seguida, adicione as especificações para a máquina virtual, incluindo o tamanho e um nome de usuário e senha. Quando terminar, clique em **Próximo**.
   
    Você usará o novo nome e senha para fazer logon no computador usando a área de trabalho remota. É uma boa ideia escrevê-los caso você esqueça. Depois de criar uma máquina virtual do Azure no Visual Studio, você pode alterar seu tamanho e outras configurações no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Se você escolher tamanhos maiores para a máquina virtual, podem aplicar encargos adicionais. Consulte [Detalhes de preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para obter mais informações.
   > 
   > 
6. Máquinas virtuais criadas no Visual Studio exigem um serviço de nuvem. Na página **Configurações de Serviço de Nuvem**, selecione um serviço de nuvem para a máquina virtual ou clique em **<Criar novo... >** na lista suspensa, se você ainda não tiver um serviço de nuvem ou se quiser usar um novo. Também é necessária uma conta de armazenamento, então escolha uma conta de armazenamento (ou crie uma nova conta de armazenamento) na caixa de listagem suspensa **Conta de armazenamento** . Para saber mais, consulte: [Introdução ao Armazenamento do Microsoft Azure](../articles/storage/common/storage-introduction.md) .
7. Se você quiser especificar uma rede virtual (que é opcional), selecione-a nas caixas de listagem suspensa da Sub-rede e Rede Virtual.
   
    Máquinas virtuais que são membros de um conjunto de disponibilidade são implantadas em domínios de falha diferentes. Consulte [Rede Virtual do Azure](https://azure.microsoft.com/services/virtual-network/) para obter mais informações.
8. Se quiser que sua máquina virtual pertença a um conjunto de disponibilidade (também opcional), selecione a caixa de seleção **Especificar um conjunto de disponibilidade** e, em seguida, escolha um conjunto de disponibilidade na caixa de listagem suspensa. Quando terminar, escolha o botão **Próximo** .
   
    Adicionar suas máquinas virtuais em um conjunto de disponibilidade ajuda seus aplicativos a permanecer disponíveis durante falhas de rede, falhas de hardware de disco local e tempo de inatividade planejado. Você precisa usar o [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=253103) para criar redes virtuais, sub-redes e definir disponibilidade. Consulte [Gerenciar a disponibilidade das máquinas virtuais](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) para obter mais informações.
9. Na página **Pontos de extremidade** , especifique os pontos de extremidade públicos que você deseja disponibilizar para os usuários de sua máquina virtual. Por exemplo, você pode optar por habilitar o HTTP (porta 80) além dos pontos de extremidade da Área de trabalho remota e do PowerShell, que são habilitados por padrão. Para adicionar um ponto de extremidade, escolha um na caixa de listagem suspensa **Nome da Porta** e, em seguida, escolha o botão **Adicionar**. Para remover um ponto de extremidade, escolha o **X** vermelho ao lado do nome na lista de pontos de extremidade.
   
    ![Página Pontos de extremidade no Assistente de máquinas virtuais.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Os pontos de extremidade que estão disponíveis dependem do serviço de nuvem que você selecionou para sua máquina virtual. Consulte [Pontos de extremidade de serviço do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) para obter mais informações.
   
   > [!NOTE]
   > Habilitar pontos de extremidade públicos disponibiliza os serviços de sua máquina virtual para a Internet. Certifique-se de instalar e configurar corretamente os pontos de extremidade e serviços em sua máquina virtual, como configurar listas de controle de acesso (ACLs) para os pontos de extremidade. Consulte [como configurar pontos de extremidade em uma Máquina Virtual](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) para obter mais informações.
   > 
   > 
10. Após terminar de configurar as definições da máquina virtual, escolha o botão **criar** para criar a máquina virtual.
    
     Quando Azure cria a máquina virtual, o **Log de atividades do Azure** mostra o andamento da operação de criação de máquina virtual.
    
     ![Log de atividades de máquina virtual - em andamento.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Para exibir apenas as informações de máquina virtual, escolha a guia **Máquinas Virtuais** no **Log de Atividades do Azure**.
    
     ![Log de atividades de máquina virtual - concluído.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Se a operação for concluída com êxito, a nova máquina virtual aparece sob o nó **Máquinas virtuais** no Gerenciador de Servidores. Faça logon nele clicando no atalho **Conectar usando a área de trabalho remota** .
    
     ![Máquina virtual que aparece no Gerenciador de Servidores.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Gerenciar suas máquinas virtuais
Na página de configuração de máquina virtual, além de desligar, conectar, atualizar e adicionar pontos de verificação para a máquina virtual selecionada, você também pode exibir ou alterar as configurações da máquina virtual. Você pode:

* Alterar o tamanho da máquina virtual.
* Selecionar a conjunto de disponibilidade para usar com a máquina virtual.
* Adicionar, remover ou alterar as configurações de pontos de extremidade públicos.
* Adicionar, remover ou configurar as extensões de máquina virtual.
* Exibir informações sobre os discos associados à máquina virtual.

### <a name="view-or-change-virtual-machine-settings"></a>Exibir ou alterar as configurações de máquina virtual
1. No Gerenciador de Servidores, escolha sua máquina virtual no nó **máquinas virtuais do Azure** .
2. No menu de atalho, escolha **Configurar** para exibir a página de configuração de máquina virtual.
   
    ![A página de configuração de máquina virtual do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Exibir informações da máquina virtual ou alterá-las.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Salvar ou restaurar o status da máquina virtual
Como você configura sua máquina virtual e instala o software, é uma boa ideia salvar seu progresso regularmente com a criação de pontos de verificação de máquina virtual. Um ponto de verificação é um instantâneo ou uma imagem do status atual da máquina virtual. Se algo der errado com a máquina virtual ou para reconfigurar a máquina virtual, você pode economizar tempo ao restaurar para um estado de ponto de verificação anterior em vez de recomeçar tudo do zero.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Criar um ponto de verificação de máquina virtual
1. No Gerenciador de Servidores, escolha sua máquina virtual no nó **máquinas virtuais do Azure** .
2. No menu de atalho, escolha **Configurar** para exibir a página de configuração de máquina virtual.
3. Na página de configuração, escolha o botão **capturar imagem** .
   
    ![Botão de captura da página de configuração do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    O diálogo **capturar Máquina Virtual** é exibido.
   
    ![Caixa de diálogo de máquina virtual de captura do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Fornece um rótulo de imagem e a descrição. Um rótulo padrão e uma descrição são fornecidos, mas você pode substituí-los pelo seu próprio rótulo se desejar.
5. Se você já tiver executado Sysprep nessa máquina virtual, selecione a caixa **executei o Sysprep na máquina virtual** .
   
    O Sysprep é uma ferramenta que, entre outras coisas, remove dados específicos de sistemas da versão da máquina virtual do Windows, facilitando o modelo que outras pessoas podem usar. Consulte [como capturar uma Máquina Virtual do Windows para usar como modelo](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) para obter mais informações. Faça backup da VM antes de executar o Sysprep.
6. Depois de terminar de definir as configurações de captura, escolha o botão **capturar** para criar o ponto de verificação.
   
    Como o Azure cria o ponto de verificação, o **Log de atividades do Azure** exibe o andamento da operação.
   
    ![Capturar um ponto de verificação de máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Quando a operação de ponto de verificação for concluída, você a verá no **Log de atividades do Azure**.
   
    ![Operação de ponto de verificação foi concluída](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Para gerenciar os pontos de verificação da máquina virtual
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Para restaurar uma máquina virtual a um estado salvo anteriormente
* Siga as etapas descritas em [passo a passo: executar restauração de nuvem das máquinas virtuais do Microsoft Azure usando o PowerShell - parte 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Para excluir um ponto de verificação
1. Vá para o [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
2. Na página de configuração da máquina virtual, escolha a guia **imagens** na parte superior da página.
3. Escolha o ponto de verificação que você deseja excluir e, em seguida, escolha o botão **Excluir** na parte inferior da página.

## <a name="shut-down-your-virtual-machine"></a>Desligar a máquina virtual
1. No Gerenciador de Servidores, escolha a máquina virtual que deseja desligar no nó **máquinas virtuais do Azure** .
2. No menu de atalho, escolha o comando **Fechar** ou escolha **Configurar** para exibir a página de configuração de máquina virtual e, em seguida, escolha o botão **Fechar**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a criação de máquinas virtuais, consulte [Criar uma máquina virtual executando Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma máquina virtual executando o Windows na Versão Prévia do Portal do Azure](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

