## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Usando as credenciais do cofre para autenticação no serviço Backup do Azure
O servidor local (cliente Windows, Windows Server ou servidor do Data Protection Manager) precisa ser autenticado em um cofre de backup para que o backup de dados possa ser feito no Azure. A autenticação é feita com as "credenciais do cofre". O conceito de credenciais do cofre é semelhante ao conceito de um arquivo de "configurações de publicação" que é usado no Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>O que é o arquivo de credencial do cofre?
O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup. O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso). A chave privada do certificado é disponibilizada para o usuário como parte do fluxo de trabalho que é fornecido como uma entrada no fluxo de trabalho de registro do computador. Isso autentica o computador para enviar os dados de backup para um cofre identificado no serviço de Backup do Azure.

As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro. É responsabilidade do usuário garantir que o arquivo de credenciais do cofre não seja comprometido. Se esse arquivo acabar em poder de qualquer usuário não autorizado, o arquivo de credenciais do cofre poderá ser usado para registrar outros computadores no mesmo cofre. No entanto, como os dados de backup são criptografados usando uma senha que pertence ao cliente, os dados de backup existentes não poderão ser comprometidos. Para atenuar esse problema, as credenciais do cofre são definidas para expirar em 48 horas. Você pode baixar as credenciais de um cofre de backup quantas vezes quiser – mas apenas o arquivo mais recente de credencial de cofre será aplicável durante o fluxo de trabalho de registro.

### <a name="download-the-vault-credential-file"></a>Baixe o arquivo de credencial do cofre
O arquivo de credencial do cofre é baixado por meio de um canal seguro no Portal do Azure. O serviço de Backup do Azure não tem conhecimento da chave privada do certificado e a chave privada não é mantida no portal nem no serviço. Use as etapas a seguir para baixar o arquivo de credenciais do cofre para um computador local.

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)
2. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup que você criou. Clique no ícone de nuvem para alternar para o modo de exibição de Início Rápido do cofre de backup.
   
   ![Visualização rápida](./media/backup-download-credentials/quickview.png)
3. Na página Início Rápido, clique em **Baixar credenciais do cofre**. O portal gera o arquivo de credencial de cofre, que fica disponível para download.
   
   ![Baixar](./media/backup-download-credentials/downloadvc.png)
4. O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de downloads da conta local ou selecione Salvar Como no menu Salvar para especificar um local para as credenciais do cofre.

### <a name="note"></a>Observação
* Certifique-se de que as credenciais do cofre sejam salvas em um local que pode ser acessado no seu computador. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
* O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro.
* O arquivo de credenciais do cofre expira após 48 horas e pode ser baixado no portal.
* Consulte as [Perguntas frequentes](../articles/backup/backup-azure-backup-faq.md) do Backup do Azure em caso de dúvidas sobre o fluxo de trabalho.

