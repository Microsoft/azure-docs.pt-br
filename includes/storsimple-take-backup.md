<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>Para fazer um backup
1. Na página **Início Rápido** do dispositivo, clique em **Adicionar uma política de backup**. Isso iniciará o assistente Adicionar Política de Backup. 
2. Na página **Definir sua política de backup** :
   
   1. Forneça um nome que contenha de 3 a 150 caracteres para sua política de backup.
   2. Selecione os volumes a serem incluídos no backup. Se você selecionar mais de um volume, esses volumes serão agrupados para criar um backup com controle de falhas.
   3. Clique no ícone de seta  ![arrow-icon](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. Na página **Definir uma agenda** :
   
   1. Selecione o tipo de backup na lista suspensa. Para obter restaurações mais rápidas, selecione **Instantâneo Local**. Para obter resiliência de dados, selecione **Instantâneo de Nuvem**.
   2. Especifique a frequência de backup em minutos, em horas, em dias ou em semanas.
   3. Selecione um tempo de retenção. As opções de retenção dependem da frequência do backup. Por exemplo, para uma política diária, a retenção pode ser especificada em semanas, enquanto a retenção para uma política mensal é especificada em meses.
   4. Selecione a hora e a data de início para a política de backup.
   5. Marque a caixa de seleção **Habilitar** para habilitar a política de backup. 
   6. Clique no ícone de verificação  ![check-icon](./media/storsimple-take-backup/HCS_CheckIcon-include.png) para salvar a política.
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      Agora você tem uma política de backup que cria backups agendados de seus dados de volume.

Você concluiu a configuração do dispositivo. 

![Vídeo disponível](./media/storsimple-take-backup/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como realizar um backup do StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).

