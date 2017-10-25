<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Para criar um volume
1. No página **Início Rápido** do dispositivo, clique em **Adicionar um volume**. Isso inicia o assistente Adicionar um volume.
2. No assistente Adicionar um volume, em **Configurações Básicas**, faça o seguinte:
   
   1. Digite um **Nome** para o seu volume.
   2. Especifique a **Capacidade Provisionada** para o seu volume em GB ou TB. A capacidade de volume deve ficar entre 1 GB e 64 TB para um dispositivo físico.
   3. Na lista suspensa, selecione o **Tipo de Uso** para o seu volume. 
   4. Se você estiver usando esse volume de dados de arquivamento, marque a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência** . Para todos os outros casos de uso, selecione **Volume em Camadas**. (Volumes em camadas eram chamados anteriormente de volumes primários).
      
        ![Adicionar volume](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Clique no ícone de seta  ![ícone-de-seta](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) para ir para a próxima página.
3. Na caixa de diálogo **Configurações Adicionais** , adicione um novo registro de controle de acesso (ACR):
   
   1. Dê um **Nome** para o seu ACR.
   2. Em **Nome do Iniciador iSCSI**, forneça o iSCSI IQN (nome qualificado) do host Windows. Se você não tiver o IQN, vá para [Obter o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. É recomendável que você habilite um backup padrão ao marcar a caixa de seleção **Habilitar um backup padrão para este volume** . O backup padrão criará uma política que é executa às 22:30 todos os dias (hora do dispositivo) e cria um instantâneo de nuvem desse volume.
      
      > [!NOTE]
      > Depois que o backup está habilitado aqui, ele não pode ser revertido. Você precisará editar o volume para modificar essa configuração.
      > 
      > 
      
        ![Adicionar volume](./media/storsimple-create-volume/AddVolume2-include.png)
4. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Será criado um volume com as configurações especificadas.

![Vídeo disponível](./media/storsimple-create-volume/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como criar um volume do StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

