---
title: 'Portal do Azure: Criar um Banco de Dados do Azure para o servidor PostgreSQL | Microsoft Docs'
description: "Guia de início rápido para criação e gerenciamento de um Banco de Dados do Azure para o servidor PostgreSQL usando a interface do usuário do portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/10/2017
ms.openlocfilehash: f806a8c1f06a58d0c8440d54c91ecfa41988d8ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL no portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este Guia de início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL usando o portal do Azure em aproximadamente cinco minutos.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure
Abra o navegador da Web e acesse o [portal](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto definido de [recursos de computação e armazenamento](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Para criar um Banco de Dados do Azure para o servidor PostgreSQL, execute as seguintes etapas:
1. Selecione o botão **Novo** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**.

    ![A opção “Banco de Dados do Azure para PostgreSQL”](./media/quickstart-create-database-portal/1-create-database.png)

3. Preencha o formulário de detalhes do novo servidor com as seguintes informações, conforme mostrado na imagem anterior:

    Configuração|Valor sugerido|Descrição
    ---|---|---
    Nome do servidor |*mypgserver-20170401*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter, pelo menos, 3 até 63 caracteres.
    Assinatura|Sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a assinatura para a qual você recebe a cobrança do recurso.
    Grupo de recursos|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.
    Logon de administrador do servidor |*mylogin*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** ou **public.** Ele não pode começar com **pg_**.
    Senha |Sua escolha | Uma nova senha para a conta do administrador do servidor. Ela deve conter de 8 a 128 caracteres. A senha deve conter caracteres das três seguintes categorias: letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, %, etc.).
    Local|A região mais próxima dos usuários| A localização mais próxima dos usuários.
    Versão do PostgreSQL|A última versão| A última versão, a menos que você tenha requisitos específicos.
    Tipo de preço  | **Básico**, **50 Unidades de Computação** **50 GB** | A camada de serviço e o nível de desempenho do novo banco de dados. Selecione **Tipo de preço**. Em seguida, selecione a guia **Básico**. Depois, selecione a extremidade esquerda do controle deslizante **Unidades de Computação** para ajustar o valor para a quantidade mínima disponível para este Guia de início rápido. Para salvar a seleção de tipo de preço, selecione **OK**. Para obter mais informações, veja a captura de tela a seguir. 
    Fixar no painel | Verificação | Permite o acompanhamento fácil do servidor na página do painel frontal do portal.

    > [!IMPORTANT]
    > O logon de administrador do servidor e a senha especificada aqui são necessários para fazer logon no servidor e em seus bancos de dados posteriormente neste Guia de início rápido. Lembre-se ou registre essas informações para o uso posterior.

    ![O painel “Tipo de preço”](./media/quickstart-create-database-portal/2-service-tier.png)

4. Selecione **Criar** para provisionar o servidor. O provisionamento pode levar até 20 minutos.

5. Na barra de ferramentas, selecione o símbolo **Notificações** para monitorar o processo de implantação.

    ![O painel “Notificações”](./media/quickstart-create-database-portal/3-notifications.png)
   
  Por padrão, um banco de dados **postgres** é criado no servidor. O banco de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado ao uso dos usuários, de utilitários e de aplicativos de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O Banco de Dados do Azure para PostgreSQL cria um firewall no nível do servidor. Ele impede que os aplicativos e as ferramentas externas se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra seja criada para abrir o firewall em endereços IP específicos. 

1. Após a conclusão da implantação, localize o servidor. Se necessário, você pode pesquisar. Por exemplo, no menu à esquerda, selecione **Todos os recursos**. Digite o nome do servidor, como o exemplo **mypgserver-20170401**, para pesquisar o servidor recém-criado. Selecione o nome do servidor na lista de resultados da pesquisa. A página **Visão geral** do servidor é aberta e oferece outras opções de configuração.
 
    ![Pesquisa de nome do servidor](./media/quickstart-create-database-portal/4-locate.png)

2. Na página do servidor, selecione **Segurança da conexão**.

    ![A configuração “Segurança da conexão”](./media/quickstart-create-database-portal/5-firewall-2.png)

3. No cabeçalho **Regras de firewall**, na coluna **Nome da Regra**, selecione a caixa de texto em branco para começar a criar a regra de firewall. 

    Para este Guia de início rápido, vamos permitir todos os endereços IP no servidor. Preencha a caixa de texto em cada coluna com os seguintes valores:

    Nome da regra | IP Inicial | IP Final 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página **Segurança da conexão**, selecione **Salvar**. Aguarde até que a notificação será exibida informando que a atualização da segurança da conexão foi concluída com êxito antes de continuar.

    > [!NOTE]
    > As conexões ao Banco de Dados do Azure para servidor PostgreSQL se comunicam pela porta 5432. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 5432 não seja permitido pelo firewall da rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter informações de conexão

Ao criar o Banco de Dados do Azure para o servidor PostgreSQL, um banco de dados padrão chamado **postgres** é criado. Para se conectar ao servidor de banco de dados, você precisa do nome do servidor completo e das credenciais de logon do administrador. Talvez você tenha anotado esses valores anteriormente no artigo do Guia de início rápido. Caso não tenha anotado, poderá encontrar facilmente o nome do servidor e as informações de logon na página **Visão geral** do servidor no portal.

Abra a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon do administrador do servidor**. Focalize o cursor em cada campo e o símbolo de cópia será exibido à direita do texto. Selecione o símbolo de cópia, conforme necessário, para copiar os valores.

 ![A página “Visão geral” do servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Conectar-se ao Banco de Dados PostgreSQL usando o psql no Cloud Shell

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Primeiro, usaremos o utilitário da linha de comando psql para ilustrar como conectar o servidor. Use um navegador da Web e o Azure Cloud Shell, conforme descrito aqui, sem precisar instalar nenhum software adicional. Se você tiver o utilitário psql instalado localmente em seu próprio computador, poderá conectar também.

1. No painel de navegação superior, selecione o símbolo de terminal para abrir o Cloud Shell.

   ![Símbolo de terminal do Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. O Cloud Shell é aberto no navegador, no qual você pode digitar comandos do shell do Bash.

   ![Prompt do Bash do Cloud Shell](./media/quickstart-create-database-portal/8-bash.png)

3. No prompt do Cloud Shell, conecte-se a um banco de dados no Banco de Dados do Azure para o servidor PostgreSQL digitando a linha de comando do psql.

    Para se conectar a um Banco de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), use o seguinte formato:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Por exemplo, o comando a seguir conecta um servidor de exemplo:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    --host | Nome do servidor | O valor do nome do servidor usado ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Nosso servidor de exemplo mostrado é **mypgserver-20170401.postgres.database.azure.com.** Use o nome de domínio totalmente qualificado (**\*.postgres.database.azure.com**), conforme mostrado no exemplo. Caso não se lembre do nome do servidor, siga as etapas da seção anterior para obter as informações de conexão. 
    --port | 5432 | A porta a ser usada ao se conectar ao Banco de Dados do Azure para o servidor PostgreSQL. 
    --username | Nome de logon do administrador do servidor |O nome de usuário de logon do administrador do servidor fornecido ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Caso não se lembre do nome de usuário, siga as etapas da seção anterior para obter as informações de conexão. O formato é *username@servername*.
    --dbname | *postgres* | O nome do banco de dados padrão gerado pelo sistema que foi criado para a primeira conexão. Mais tarde, você criará seu próprio banco de dados.

    Depois de executar o comando do psql com seus próprios valores de parâmetro, você deverá inserir a senha do administrador do servidor. A senha é a mesma fornecida quando o servidor foi criado. 

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    Senha | Sua senha de administrador | Os caracteres da senha digitados não são mostrados no prompt do Bash. Depois de digitar todos os caracteres, selecione a tecla **Enter** para se autenticar e conectar.

    Depois que você se conectar, o utilitário psql exibirá um prompt do postgres no qual você digitará os comandos do sql. Na saída de conexão inicial, um aviso pode ser exibido, pois o psql no Cloud Shell pode ter uma versão diferente da versão do Banco de Dados do Azure para o servidor PostgreSQL. 
    
    Exemplo de saída psql:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Se o firewall não está configurado para permitir o endereço IP do Cloud Shell, ocorre o seguinte erro:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL: SSL connection is required.Specify SSL options and retry.
    > 
    > Para resolver o erro, verifique se a configuração do servidor corresponde às etapas da seção “Configurar uma regra de firewall no nível do servidor” deste artigo.

4. Crie um banco de dados em branco no prompt digitando o seguinte comando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    O comando pode levar alguns minutos para ser concluído. 

5. No prompt, execute o seguinte comando para mudar as conexões para o banco de dados **mypgsqldb** recém-criado:
    ```bash
    \c mypgsqldb
    ```

6. Digite `\q` e, em seguida, selecione a tecla **Enter** para sair do psql. Feche o Cloud Shell quando terminar.

Agora, você está conectado ao Banco de Dados do Azure para o servidor PostgreSQL e criou um banco de dados do usuário em branco. Continue na próxima seção para se conectar usando outra ferramenta comum, pgAdmin.

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>Conectar-se ao Banco de Dados PostgreSQL usando o pgAdmin

Para se conectar ao servidor PostgreSQL do Azure usando a ferramenta da GUI pgAdmin:
1. Abra o aplicativo pgAdmin no computador cliente. Instale o pgAdmin por meio do [site do pgAdmin](http://www.pgadmin.org/).

2. Na página do painel, na seção **Links Rápidos**, selecione o símbolo **Adicionar Novo Servidor**.

3. Na caixa de diálogo **Criar – Servidor**, na guia **Geral**, insira um nome amigável exclusivo para o servidor, como **Servidor PostgreSQL do Azure**.

    ![A guia “Geral”](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. Na caixa de diálogo **Criar – Servidor**, na guia **Conexão**, use as configurações especificadas e, em seguida, selecione **Salvar**.

   ![A guia “Conexão”](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    parâmetro pgAdmin |Valor sugerido|Descrição
    ---|---|---
    Nome/endereço do host | Nome do servidor | O valor do nome do servidor usado ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Nosso servidor de exemplo é **mypgserver-20170401.postgres.database.azure.com.** Use o nome de domínio totalmente qualificado (**\*.postgres.database.azure.com**), conforme mostrado no exemplo. Caso não se lembre do nome do servidor, siga as etapas da seção anterior para obter as informações de conexão. 
    Porta | 5432 | A porta a ser usada ao se conectar ao Banco de Dados do Azure para o servidor PostgreSQL. 
    Banco de dados de manutenção | *postgres* | O nome do banco de dados padrão gerado pelo sistema.
    Nome de Usuário | Nome de logon do administrador do servidor | O nome de usuário de logon do administrador do servidor fornecido ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Caso não se lembre do nome de usuário, siga as etapas da seção anterior para obter as informações de conexão. O formato é *username@servername*.
    Senha | Sua senha de administrador | A senha que você escolheu ao criar o servidor anteriormente neste Guia de início rápido.
    Função | Deixar em branco | Não é necessário fornecer um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | Obrigatório | Por padrão, todos os servidores PostgreSQL do Azure são criados com a imposição de SSL ativada. Para desligar a imposição de SSL, consulte [Impor SSL](./concepts-ssl-connection-security.md).
    
5. Selecione **Salvar**.

6. No painel **Navegador** à esquerda, expanda o nó **Servidores**. Selecione o servidor, por exemplo, **Servidor PostgreSQL do Azure**. Clique nele para se conectar a ele.

7. Expanda o nó do servidor e expanda **Bancos de Dados** nele. A lista deve incluir o banco de dados *postgres* existente e os bancos de dados de usuário recém-criados, como **mypgsqldb**, que criamos na seção anterior. Observe que você pode criar vários bancos de dados por servidor com o Banco de Dados do Azure para PostgreSQL.

8. Clique com o botão direito do mouse em **Bancos de Dados**, escolha o menu **Criar** e, em seguida, selecione **Banco de Dados**.

9. Digite um nome de banco de dados de sua escolha no campo **Banco de Dados**, como **mypgsqldb**, conforme mostrado no exemplo.

10. Selecione o **Proprietário** do banco de dados na caixa de listagem. Escolha o nome de logon do administrador do servidor, como nosso exemplo, **mylogin**.

11. Selecione **Salvar** para criar um novo banco de dados em branco.

12. No painel **Navegador**, veja o banco de dados criado na lista de bancos de dados sob o nome do servidor.

    ![O painel “Navegador”](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no Guia de início rápido usando uma das duas maneiras. Exclua o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o recurso de servidor individual.

> [!TIP]
> Os outros Guias de início rápido desta coleção se baseiam neste Guia de início rápido. Se você pretende continuar trabalhando com Guias de início rápido, não limpe os recursos criados neste Guia de início rápido. Caso contrário, siga estas etapas para excluir os recursos que foram criados por este Guia de início rápido no portal.

Para excluir o grupo de recursos inteiro, incluindo o servidor recém-criado:
1. Localize o grupo de recursos no portal. No menu à esquerda, selecione **Grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, como nosso exemplo, **myresourcegroup**.

2. Na página do grupo de recursos, selecione **Excluir**. Digite o nome do grupo de recursos, como nosso exemplo **myresourcegroup**, na caixa de texto para confirmar a exclusão. Selecione **Excluir**.

Para excluir apenas o servidor recém-criado:
1. Localize o servidor no portal, caso você não esteja com ele aberto. No menu à esquerda, selecione **Todos os recursos**. Em seguida, pesquise o servidor criado.

2. Na página **Visão geral**, selecione **Excluir**.

    ![O botão “Excluir”](./media/quickstart-create-database-portal/12-delete.png)

3. Confirme o nome do servidor que deseja excluir e exiba sob ele os bancos de dados que são afetados. Digite o nome do servidor na caixa de texto, como nosso exemplo **mypgserver-20170401**. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
