<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Para adicionar um contêiner de volume
1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e clique na guia **Contêineres de Volume**.
2. Clique em **Adicionar** na parte inferior da página. Na caixa de diálogo **Criar contêiner de volume** , faça o seguinte:
   
   1. Forneça um **Nome** exclusivo para seu contêiner do volume. O nome pode conter no máximo 32 caracteres.
   2. Selecione uma **Conta de Armazenamento** a ser associada a esse contêiner de volume. Você pode escolher uma conta de armazenamento existente na mesma assinatura ou selecionar **Adicionar mais** para selecionar uma conta de armazenamento de outra assinatura. Você também pode escolher a conta de armazenamento que foi gerada pela primeira vez quando o serviço foi criado.
   3. Especifique a largura de banda como **Ilimitada** se desejar consumir toda largura de banda disponível ou **Personalizada** para empregar controles de largura de banda. Para uma largura de banda personalizada, forneça um valor entre 1 e 1000 Mbps. Para alocar largura de banda com base em uma agenda, você pode **Selecionar um modelo de largura de banda**.
   4. Recomendamos que você mantenha a opção **Habilitar Criptografia de Armazenamento em Nuvem** marcada para criptografar os dados que vão para a nuvem. Desabilite a criptografia somente se você estiver usando outros meios para criptografar seus dados. Você não pode modificar a configuração de criptografia quando o contêiner de volume tiver sido criado.
   5. Forneça uma **Chave de Criptografia de Armazenamento em Nuvem** que contenha entre 8 e 32 caracteres. O dispositivo usa essa chave para acessar os dados criptografados. No campo **Confirmar Chave de Criptografia de Armazenamento em Nuvem** , insira a chave de criptografia de armazenamento em nuvem novamente para confirmá-la. 
   6. Clique na seta para prosseguir para a próxima página.
      
      ![Criar contêiner de volume com o modelo de largura de banda 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Se você especificou **Selecionar um modelo de largura de banda**, escolha um modelo existente de largura de banda na lista suspensa. Examine as configurações de agenda e clique no ícone de verificação ![ícone de verificação](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Criar contêiner de volume com o modelo de largura de banda 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

O contêiner de volume será salvo, e o contêiner de volume recém-criado será listado na página **Contêiner de volume** .

