
1. Para escalonar privilégios, digite:
   
        sudo -s
   
    Digite sua senha.
2. Para instalar o MySQL Community Server edition, digite:
   
        zypper install mysql-community-server
   
    Aguarde enquanto o MySQL é baixado e instalado.
3. Para definir o MySQL para ser iniciado quando o sistema for inicializado, digite:
   
        insserv mysql
4. Inicie manualmente o daemon do MySQL (mysqld) com este comando:
   
        rcmysql start
   
    Para verificar o status do daemon do MySQL, execute:
   
        rcmysql status
   
    Para interromper o daemon do MySQL, digite:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > Após a instalação, a senha da raiz do MySQL é vazia por padrão. Recomendamos a execução de **mysql\_secure\_installation**, um script que ajuda a proteger o MySQL. O script solicita que você altere a senha raiz do MySQL, remova as contas anônimas de usuários, desabilite os logins de raiz remota, remova bancos de dados de teste e recarregue a tabela de privilégios. É recomendável que você responda Sim para todas essas opções e altere a senha raiz.
   > 
   > 
5. Digite isto para executar o script de instalação do MySQL:
   
        mysql_secure_installation
6. Faça logon no MySQL:
   
        mysql -u root -p
   
    Digite a senha raiz de MySQL (que você alterou na etapa anterior) e será exibido um prompt onde você poderá executar instruções SQL para interagir com o banco de dados.
7. Para criar um novo usuário do MySQL, execute o seguinte no prompt **mysql>**:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Observe que o ponto e vírgula (;) no final das linhas são cruciais para encerrar os comandos.
8. Para criar um banco de dados e conceder as `mysqluser` permissões de usuário a ele, emita os seguintes comandos:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Observe que as senhas e nomes de usuário do banco de dados só são usadas pelos scripts de conexão com o banco de dados.  Nomes de conta de usuário do banco de dados não representam, necessariamente, contas de usuário reais no sistema.
9. Para fazer logon em outro computador, digite:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    em que `ip-address` é o endereço IP do computador do qual você se conectará ao MySQL.
10. Para sair do utilitário de administração de banco de dados MySQL, digite:
    
        quit

## <a name="add-an-endpoint"></a>Adicionar um ponto de extremidade
1. Após a instalação do MySQL, você precisará configurar um ponto de extremidade para acessar remotamente o MySQL. Faça logon no [Portal Clássico do Azure][AzurePortal]. Clique em **Máquinas Virtuais**, clique no nome de sua nova máquina virtual e clique em **Pontos de Extremidade**.
2. Clique em **Adicionar** na parte inferior da página.
3. Adicione um ponto de extremidade chamado "MySQL" com o protocolo **TCP** e as portas **Pública** e **Privada** configuradas como "3306".
4. Para conectar-se remotamente à máquina virtual do seu computador, digite:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Por exemplo, usando a máquina virtual criada neste tutorial, digite este comando:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
