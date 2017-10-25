## <a name="deploy-template-from-cloud-shell"></a>Implantar o modelo do Cloud Shell

Você pode usar o [Cloud Shell](../articles/cloud-shell/overview.md) para implantar o modelo. No entanto, você deve carregar o modelo primeiro para o compartilhamento de arquivos do seu Cloud Shell. Se você ainda não usou o Cloud Shell, confira [Visão geral do Azure Cloud Shell](../articles/cloud-shell/overview.md) para saber mais sobre como configurá-lo.

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O nome padrão é `cloud-shell-storage-<region>`.

   ![Escolha o grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selecione a conta de armazenamento do Cloud Shell.

   ![Escolher conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecionar **Arquivos**.

   ![Selecionar arquivos](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Selecione o compartilhamento de arquivos para o Cloud Shell. O nome padrão é `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selecionar compartilhamento de arquivos](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Selecione **Adicionar diretório**.

   ![Adicionar diretório](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Dê a ele o nome de **modelos**e selecione **OK**.

   ![Nomear diretório](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Selecione o novo diretório.

   ![Selecionar diretório](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Escolha **Carregar**.

   ![Selecionar Carregar](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Localizar e carregar o modelo.

   ![Carregar arquivo](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Abra o prompt.

   ![Abrir Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
