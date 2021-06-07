---
title: 'Início Rápido: Criar servidor – portal do Azure – Servidor Flexível do Banco de Dados do Azure para PostgreSQL'
description: Guia de início rápido para criar e gerenciar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL usando a interface do usuário do portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604746"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Início Rápido: Criar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL no portal do Azure

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este guia de início rápido mostra como criar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL usando o portal do Azure em aproximadamente cinco minutos.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Abra o navegador da Web e acesse o [portal](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto configurado de [recursos de computação e armazenamento](./concepts-compute-storage.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../../azure-resource-manager/management/overview.md).

Para criar um Banco de Dados do Azure para o servidor PostgreSQL, execute as seguintes etapas:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="O Banco de Dados do Azure para PostgreSQL no menu":::

3. Selecione a opção de implantação **Servidor flexível**.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Selecionar a opção de implantação Servidor Flexível do Banco de Dados do Azure para PostgreSQL":::

4. Preencha o formulário **Básico** com as seguintes informações:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Criar um servidor":::

    Configuração|Valor sugerido|Descrição
    ---|---|---
    Subscription|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Caso você tenha várias assinaturas, escolha a assinatura na qual você deseja receber a cobrança do recurso.
    Resource group|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.
    Nome do servidor |*mydemoserver*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter, pelo menos, 3 até 63 caracteres.
    Nome de usuário do administrador |*myadmin*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** ou **public**. Ele não pode começar com **pg_** .
    Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).
    Location|A região mais próxima dos usuários| A localização mais próxima dos usuários.
    Versão|A última versão principal| A última versão principal do PostgreSQL, a menos que você tenha requisitos específicos.
    Computação + armazenamento | **Uso Geral**, **4 vCores**, **512 GB**, **7 dias** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. *Uso Geral*, *4 vCores*, *512 GB*, and *7 dias* são os valores padrão de **Camada de computação**, **vCore**, **Armazenamento** e **Período de Retenção de Backup**. Você pode deixar esses controles deslizantes como estão ou ajustá-los. Para salvar a seleção desse tipo de preço, selecione **OK**. A captura de tela a seguir demonstra essas seleções.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="O painel Tipo de preço":::
    
5. Como configurar as opções de Rede

    Na guia Rede, você pode escolher como o servidor poderá ser acessado. O Banco de Dados do Azure para PostgreSQL cria um firewall no nível do servidor. Ele impede que os aplicativos e as ferramentas externas se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra seja criada para abrir o firewall em endereços IP específicos. Recomendamos tornar o servidor publicamente acessível:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="O painel Rede":::

    E, depois, restringi-lo ao seu endereço IP do cliente:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Selecionar Adicionar endereço IP do cliente atual":::

6. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para provisionar o servidor. Esta operação pode levar alguns minutos.

7. Na barra de ferramentas, selecione o ícone (sino) **Notificações** para monitorar o processo de implantação. Depois que a implantação é feita, você pode selecionar **Fixar no painel**, que cria um bloco para esse servidor no seu painel do portal do Azure como um atalho para a página **Visão geral** do servidor. A opção **Ir para recurso** abre a página **Visão geral** do servidor.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="O painel Notificações":::

   Por padrão, um banco de dados **postgres** é criado no servidor. O banco de dados [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) é um banco de dados padrão destinado ao uso dos usuários, de utilitários e de aplicativos de terceiros. (O outro banco de dados padrão é o **azure_maintenance**. Sua função é separar os processos de serviço gerenciado das ações do usuário. Não é possível acessar este banco de dados).

    > [!NOTE]
    > As conexões ao Banco de Dados do Azure para servidor PostgreSQL se comunicam pela porta 5432. Quando você tenta se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 5432 não seja permitido pelo firewall da rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter informações de conexão

Ao criar o Banco de Dados do Azure para o servidor PostgreSQL, um banco de dados padrão chamado **postgres** é criado. Para se conectar ao servidor de banco de dados, você precisa do nome do servidor completo e das credenciais de logon do administrador. Talvez você tenha anotado esses valores anteriormente no artigo do Guia de início rápido. Caso não tenha anotado, poderá encontrar facilmente o nome do servidor e as informações de logon na página **Visão geral** do servidor no portal.

Abra a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon do administrador do servidor**. Focalize o cursor em cada campo e o símbolo de cópia será exibido à direita do texto. Selecione o símbolo de cópia, conforme necessário, para copiar os valores.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="A página Visão geral do servidor":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Conectar-se ao banco de dados PostgreSQL usando psql

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao servidor PostgreSQL do Azure.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para servidor PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Depois que você se conectar, o utilitário psql exibirá um prompt do postgres no qual você digitará os comandos do sql. Na saída de conexão inicial, um aviso pode ser exibido, pois o psql que você está usando pode ter uma versão diferente da versão Banco de Dados do Azure para o servidor PostgreSQL.

   Exemplo de saída psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
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

Você está conectado ao Banco de Dados do Azure para servidor PostgreSQL via psql e criou um banco de dados do usuário em branco.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos criados no Guia de início rápido usando uma das duas maneiras. Você pode excluir o grupo de recursos do Azure, que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o recurso de servidor.

> [!TIP]
> Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você pretende continuar trabalhando com Guias de início rápido, não limpe os recursos criados neste Guia de início rápido. Caso contrário, siga estas etapas para excluir os recursos que foram criados por este Guia de início rápido no portal.

Para excluir o grupo de recursos inteiro, incluindo o servidor recém-criado:

1. Localize o grupo de recursos no portal. No menu à esquerda, selecione **Grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, como o exemplo, **myresourcegroup**.

2. Na página do grupo de recursos, selecione **Excluir**. Insira o nome do grupo de recursos, como o exemplo **myresourcegroup**, na caixa de texto para confirmar a exclusão. Selecione **Excluir**.

Para excluir apenas o servidor recém-criado:

1. Localize o servidor no portal, caso você não esteja com ele aberto. No menu à esquerda, selecione **Todos os recursos**. Em seguida, procure o servidor que você criou.

2. Na página **Visão Geral**, selecione **Excluir**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="O botão Excluir":::

3. Confirme o nome do servidor que deseja excluir e veja embaixo do nome dele os bancos de dados que são afetados. Insira o nome do servidor na caixa de texto, como o exemplo **mydemoserver**. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Implantar um aplicativo Django usando o Serviço de Aplicativo e o PostgreSQL](tutorial-django-app-service-postgres.md)