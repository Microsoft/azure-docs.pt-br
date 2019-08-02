---
title: Guia de início rápido – Criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando o portal do Azure
description: Guia de início rápido para criar e gerenciar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a interface do usuário do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/25/2019
ms.openlocfilehash: 152e93fefecaeaab3502ad5d974ba923727f919b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443123"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Início Rápido: Criar um Banco de Dados do Azure para o servidor PostgreSQL no portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este Guia de início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL usando o portal do Azure em aproximadamente cinco minutos.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Abra o navegador da Web e acesse o [portal](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto configurado de [recursos de computação e armazenamento](./concepts-pricing-tiers.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Para criar um Banco de Dados do Azure para o servidor PostgreSQL, execute as seguintes etapas:
1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**.

    ![O "Banco de Dados do Azure para PostgreSQL" no menu](./media/quickstart-create-database-portal/1-create-database.png)

3. Selecione a opção de implantação **Servidor único**.

   ![Selecione o Banco de Dados do Azure para PostgreSQL – Opção de implantação de servidor único](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Preencha o formulário **Básico** com as seguintes informações:

    ![Criar um servidor](./media/quickstart-create-database-portal/create-basics.png)

    Configuração|Valor sugerido|DESCRIÇÃO
    ---|---|---
    Assinatura|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a assinatura para a qual você recebe a cobrança do recurso.
    Grupo de recursos|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.
    Nome do servidor |*mydemoserver*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter, pelo menos, 3 até 63 caracteres.
    Fonte de dados | *Nenhum* | Selecione *Nenhum* para criar um novo servidor do zero. (Você selecionaria *Backup* se estivesse criando um servidor de um backup de replicação geográfica de um Banco de Dados do Azure para servidor PostgreSQL existente).
    Nome de usuário do administrador |*myadmin*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** ou **public**. Ele não pode começar com **pg_** .
    Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).
    Local padrão|A região mais próxima dos usuários| A localização mais próxima dos usuários.
    Versão|A última versão principal| A última versão principal do PostgreSQL, a menos que você tenha requisitos específicos.
    Computação + armazenamento | **Uso Geral**, **Gen 5**, **2 vCores**, **5 GB**, **7 dias**, **Com redundância geográfica** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. Em seguida, selecione a guia **Uso Geral**. *Gen 5*, *4 vCores*, *100 GB* e *7 dias* são os valores padrão de **Geração da Computação**, **vCore**, **Armazenamento** e **Período de Retenção de Backup**. Você pode deixar esses controles deslizantes como estão ou ajustá-los. Para habilitar os backups do servidor em armazenamento com redundância geográfica, selecione **Redundância Geográfica** das **Opções de Redundância de Backup**. Para salvar a seleção desse tipo de preço, selecione **OK**. A captura de tela a seguir demonstra essas seleções.

   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que servidores criados no tipo de preço Básico não podem ser dimensionados mais tarde para Uso Geral ou Otimizado para Memória. Veja a [página de preço](https://azure.microsoft.com/pricing/details/postgresql/) para obter mais informações.
   > 

    ![O painel “Tipo de preço”](./media/quickstart-create-database-portal/2-pricing-tier.png)

5. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para provisionar o servidor. Esta operação pode levar alguns minutos.

6. Na barra de ferramentas, selecione o ícone (sino) **Notificações** para monitorar o processo de implantação. Depois que a implantação é feita, você pode selecionar **Fixar no painel**, que cria um bloco para esse servidor no seu painel do portal do Azure como um atalho para a página **Visão geral** do servidor. A opção **Ir para recurso** abre a página **Visão geral** do servidor.

    ![O painel “Notificações”](./media/quickstart-create-database-portal/3-notifications.png)
   
   Por padrão, um banco de dados **postgres** é criado no servidor. O banco de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado ao uso dos usuários, de utilitários e de aplicativos de terceiros. (O outro banco de dados padrão é o **azure_maintenance**. Sua função é separar os processos de serviço gerenciado das ações do usuário. Não é possível acessar este banco de dados).

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O Banco de Dados do Azure para PostgreSQL cria um firewall no nível do servidor. Ele impede que os aplicativos e as ferramentas externas se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra seja criada para abrir o firewall em endereços IP específicos. 

1. Após a conclusão da implantação, localize o servidor. Se necessário, você pode pesquisar. Por exemplo, no menu à esquerda, selecione **Todos os recursos**. Digite o nome do servidor, como o exemplo **mydemoserver**, para pesquisar o servidor recém-criado. Selecione o nome do servidor na lista de resultados da pesquisa. A página **Visão geral** do servidor é aberta e oferece outras opções de configuração.
 
    ![Pesquisa de nome do servidor](./media/quickstart-create-database-portal/4-locate.png)

2. Na página do servidor, selecione **Segurança da conexão**.

3. Nas **Regras de firewall**, na coluna **Nome da Regra**, selecione a caixa de texto em branco para começar a criar a regra de firewall. 

   Preencha as caixas de texto com um nome e o intervalo IP de início e término dos clientes que acessarão o servidor. Se for um único IP, use o mesmo valor para o IP inicial e o IP final.

   ![Definir regras de firewall](./media/quickstart-create-database-portal/5-firewall-2.png)
     

4. Na barra de ferramentas superior da página **Segurança da conexão**, selecione **Salvar**. Aguarde até que a notificação será exibida informando que a atualização da segurança da conexão foi concluída com êxito antes de continuar.

    > [!NOTE]
    > As conexões ao Banco de Dados do Azure para servidor PostgreSQL se comunicam pela porta 5432. Quando você tenta se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 5432 não seja permitido pelo firewall da rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter informações de conexão

Ao criar o Banco de Dados do Azure para o servidor PostgreSQL, um banco de dados padrão chamado **postgres** é criado. Para se conectar ao servidor de banco de dados, você precisa do nome do servidor completo e das credenciais de logon do administrador. Talvez você tenha anotado esses valores anteriormente no artigo do Guia de início rápido. Caso não tenha anotado, poderá encontrar facilmente o nome do servidor e as informações de logon na página **Visão geral** do servidor no portal.

Abra a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon do administrador do servidor**. Focalize o cursor em cada campo e o símbolo de cópia será exibido à direita do texto. Selecione o símbolo de cópia, conforme necessário, para copiar os valores.

 ![A página “Visão geral” do servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-using-psql"></a>Conectar-se ao banco de dados PostgreSQL usando psql

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao servidor PostgreSQL do Azure.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para servidor PostgreSQL
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se você preferir usar um caminho de URL para se conectar ao Postgres, codifique a URL de entrada @ do nome de usuário com `%40`. Por exemplo, a cadeia de conexão para psql seria, 
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

   Depois que você se conectar, o utilitário psql exibirá um prompt do postgres no qual você digitará os comandos do sql. Na saída de conexão inicial, um aviso pode ser exibido, pois o psql que você está usando pode ter uma versão diferente da versão Banco de Dados do Azure para o servidor PostgreSQL. 

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
   > Se o firewall não está configurado para permitir o endereço IP do seu cliente, ocorre o seguinte erro:
   > 
   > "psql: FATAL: nenhuma entrada pg_hba.conf para o host `<IP address>`, usuário "myadmin", banco de dados "postgres", SSL em FATAL: Uma conexão SSL é necessária. Especifique as opções de SSL e tente novamente.
   > 
   > Confirme se o que IP do seu cliente é permitido na etapa de regras de firewall acima.

2. Crie um banco de dados chamado “mypgsqldb” em branco no prompt digitando o seguinte comando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. No prompt, execute o seguinte comando para mudar as conexões para o banco de dados **mypgsqldb** recém-criado:
    ```bash
    \c mypgsqldb
    ```

4. Digite `\q` e selecione a tecla Enter para sair do psql. 

Você está conectado ao Banco de Dados do Azure para servidor PostgreSQL via psql e criou um banco de dados do usuário em branco. Continue na próxima seção para se conectar usando outra ferramenta comum, pgAdmin.

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Conectar-se ao Servidor PostgreSQL usando pgAdmin

pgAdmin é uma ferramenta de software livre usada com PostgreSQL. Instale o pgAdmin por meio do [site do pgAdmin](https://www.pgadmin.org/). A versão de pgAdmin que você está usando pode ser diferente da que é usada neste Início Rápido. Leia a documentação de pgAdmin se precisar de orientação adicional.

1. Abra o aplicativo pgAdmin no computador cliente.

2. Na barra de ferramentas, vá para **Objeto**, passe o mouse sobre **Criar** e selecione **Servidor**.

3. Na caixa de diálogo **Criar – Servidor**, na guia **Geral**, insira um nome amigável exclusivo para o servidor, como **mydemoserver**.

    ![A guia “Geral”](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. Na caixa de diálogo **Criar - Servidor** da guia **Conexão**, preencha a tabela de configurações.

   ![A guia “Conexão”](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    parâmetro pgAdmin |Valor|DESCRIÇÃO
    ---|---|---
    Nome/endereço do host | Nome do servidor | O valor do nome do servidor usado ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Nosso servidor de exemplo é **mydemoserver.postgres.database.azure.com.** Use o nome de domínio totalmente qualificado ( **\*.postgres.database.azure.com**), conforme mostrado no exemplo. Caso não se lembre do nome do servidor, siga as etapas da seção anterior para obter as informações de conexão. 
    Porta | 5432 | A porta a ser usada ao se conectar ao Banco de Dados do Azure para o servidor PostgreSQL. 
    Banco de dados de manutenção | *postgres* | O nome do banco de dados padrão gerado pelo sistema.
    Nome de Usuário | Nome de logon do administrador do servidor | O nome de usuário de logon do administrador do servidor fornecido ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Caso não se lembre do nome de usuário, siga as etapas da seção anterior para obter as informações de conexão. O formato é *nome de usuário\@nome do servidor*.
    Senha | Sua senha de administrador | A senha que você escolheu ao criar o servidor anteriormente neste Guia de início rápido.
    Função | Deixar em branco | Não é necessário fornecer um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | *Exigir* | Você pode definir o modo SSL na guia SSL do pgAdmin. Por padrão, todos os Bancos de Dados do Azure para servidores PostgreSQL são criados com a imposição de SSL ligada. Para desligar a imposição de SSL, confira [Imposição de SSL](./concepts-ssl-connection-security.md).
    
5. Clique em **Salvar**.

6. No painel **Navegador** à esquerda, expanda o nó **Servidores**. Selecione o servidor, por exemplo, **mydemoserver**. Clique nele para se conectar a ele.

7. Expanda o nó do servidor e expanda **Bancos de Dados** nele. A lista deve conter o banco de dados *postgres* existente e outros bancos de dados criados. Você pode criar vários bancos de dados por servidor com o Banco de Dados do Azure para PostgreSQL.

8. Clique com o botão direito do mouse em **Bancos de Dados**, escolha o menu **Criar** e, em seguida, selecione **Banco de Dados**.

9. Digite um nome de banco de dados de sua escolha no campo **Banco de Dados**, como **mypgsqldb2**.

10. Selecione o **Proprietário** do banco de dados na caixa de listagem. Escolha o nome de logon do administrador do servidor, como o exemplo, **myadmin**.

    ![Criar um banco de dados em pgadmin](./media/quickstart-create-database-portal/11-pgadmin-database.png)

11. Selecione **Salvar** para criar um novo banco de dados em branco.

12. No painel **Procurar**, veja o banco de dados criado na lista de bancos de dados com o nome do servidor.


## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no Guia de início rápido usando uma das duas maneiras. Você pode excluir o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o recurso de servidor.

> [!TIP]
> Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você pretende continuar trabalhando com Guias de início rápido, não limpe os recursos criados neste Guia de início rápido. Caso contrário, siga estas etapas para excluir os recursos que foram criados por este Guia de início rápido no portal.

Para excluir o grupo de recursos inteiro, incluindo o servidor recém-criado:
1. Localize o grupo de recursos no portal. No menu à esquerda, selecione **Grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, como o exemplo, **myresourcegroup**.

2. Na página do grupo de recursos, selecione **Excluir**. Digite o nome do grupo de recursos, como o exemplo **myresourcegroup**, na caixa de texto para confirmar a exclusão. Selecione **Excluir**.

Para excluir apenas o servidor recém-criado:
1. Localize o servidor no portal, caso você não esteja com ele aberto. No menu à esquerda, selecione **Todos os recursos**. Em seguida, pesquise o servidor criado.

2. Na página **Visão geral**, selecione **Excluir**.

    ![O botão “Excluir”](./media/quickstart-create-database-portal/12-delete.png)

3. Confirme o nome do servidor que deseja excluir e veja embaixo do nome dele os bancos de dados que são afetados. Digite o nome do servidor na caixa de texto, como o exemplo **mydemoserver**. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
