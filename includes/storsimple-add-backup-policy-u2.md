<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de backup do StorSimple
1. Na página **Início Rápido** do dispositivo, clique na guia **Políticas de Backup**. Isso o levará para a página **Políticas de Backup** .
2. Na parte inferior da página, clique em **Adicionar** para iniciar o assistente Adicionar Política de Backup.
   
    ![Adicionar uma política 1 de backup](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. Na caixa de diálogo **Adicionar Política de Backup**, em **Definir sua política de backup**, faça o seguinte:
   
   1. Especifique um nome que contenha de 3 a 150 caracteres para sua política de backup.
   2. Clique nas caixas de seleção para atribuir um ou mais volumes a esta política de backup. Observe que você não pode selecionar volumes que usam provedores de serviço de nuvem diferentes. Se estiver usando vários provedores de serviços de nuvem, com base na sua primeira seleção, a lista suspensa mostrará os volumes que pertencem apenas a esse provedor de serviços de nuvem. Isso permitirá que você agrupe os volumes pertencentes a um único provedor de serviços de nuvem em um instantâneo.
   3. Clique no ícone de seta  ![ícone de seta](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) para ir para a próxima página.
      
      ![Adicionar uma política 2 de backup](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. Em **Definir um agendamento**, faça o seguinte:
   
   1. Na caixa **Tipo de Backup**, selecione **Instantâneo de Nuvem** ou **Instantâneo Local** na lista suspensa.
   2. Indique a frequência dos backups (Especifique um número e escolha **Dias** ou **Semanas** na lista suspensa).
   3. Insira um agendamento de retenção.
   4. Insira o dia e a hora de início da política de backup.  
   5. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) para salvar a política.

A política recém-adicionada será exibida no modo de exibição tabular na página **Políticas de Backup** .

