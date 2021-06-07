---
title: 'Início Rápido: Criar servidor – Portal do Azure – Banco de Dados do Azure para PostgreSQL – servidor único'
description: Neste guia de início rápido, você criará e gerenciará um servidor do Banco de Dados do Azure para PostgreSQL usando o portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 6b5b145e84a3844b646ec9adfb46f8a221c6d0a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606497"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Início Rápido: Criar um servidor do Banco de Dados do Azure para PostgreSQL usando o portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este guia de início rápido mostra como criar um Banco de Dados do Azure para PostgreSQL e conectar-se a ele.

## <a name="prerequisites"></a>Pré-requisitos
Será necessário ter uma assinatura ativa do Azure. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL
Acesse o [portal do Azure](https://portal.azure.com/) para criar um banco de dados de Servidor Único do Banco de Dados do Azure para PostgreSQL. Pesquise e selecione *servidores do Banco de Dados do Azure para PostgreSQL*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Localize Banco de Dados do Azure para PostgreSQL.":::

1. Selecione **Adicionar**.

2. Na página Criar um Banco de Dados do Azure para PostgreSQL, selecione **Servidor único**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Selecionar servidor único":::

3. Preencha o formulário **Informações Básicas** com as informações a seguir.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Captura de tela que mostra a guia Informações Básicas para criar um servidor único.":::

   |Configuração|Valor sugerido|Descrição|
   |:---|:---|:---|
   |Subscription|nome da sua assinatura|selecione a Assinatura do Azure desejada.|
   |Resource group|*myresourcegroup*| um grupo de recursos novo ou existente da sua assinatura.|
   |Nome do servidor |*mydemoserver*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido por você. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa conter de 3 a 63 caracteres.|
   |Fonte de dados | Nenhum | Selecione **Nenhum** para criar um novo servidor do zero. Selecione **Backup** somente se você estivesse restaurando de um backup geográfico de um servidor existente.|
   |Nome de usuário do administrador |*myadmin*| Insira o nome do usuário administrador do servidor. Ele não pode começar com **pg_** e estes valores não são permitidos: **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** ou **public**.|
   |Senha |sua senha| Uma nova senha para o usuário administrador do servidor. Ela precisa conter de 8 a 128 caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, $, # e %).|
   |Location|o local desejado| Selecione uma localização na lista suspensa.|
   |Versão|A última versão principal| A última versão principal do PostgreSQL, a menos que você tenha requisitos específicos.|
   |Computação + armazenamento | *usar os padrões*| O tipo de preço padrão é **Uso Geral** com **4 vCores** e armazenamento de **100 GB**. A retenção de backup é definida como **7 dias** com opção de backup **Geograficamente Redundante**.<br/>Saiba mais sobre os [preços](https://azure.microsoft.com/pricing/details/postgresql/server/) e atualize os padrões, se necessário.|


   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que os servidores criados no tipo de preço Básico não podem ser escalados posteriormente para Uso Geral ou Otimizado para Memória.

5. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para provisionar o servidor. Essa operação poderá levar alguns minutos.
    > [!NOTE]
    > Um banco de dados vazio, **postgres**, é criado. Você também encontrará um banco de dados **azure_maintenance** que é usado para separar os processos de serviço gerenciado das ações do usuário. Não é possível acessar o banco de dados **azure_maintenance**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="implantação bem-sucedida.":::

[Está com problemas? Fale conosco.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall
Por padrão, o servidor que você cria não é publicamente acessível. Você precisa conceder permissões ao seu endereço IP. Acesse o recurso de servidor no portal do Azure e selecione **Segurança da conexão** no menu do lado esquerdo do recurso de servidor. Caso não tenha certeza de como localizar seu recurso, confira [Abrir recursos](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Captura de tela que mostra as regras de firewall da segurança de conexão.":::

Selecione **Adicionar o endereço IP do cliente atual** e escolha **Salvar**. Adicione mais endereços IP ou forneça um intervalo de IP para se conectar ao seu servidor nesses endereços IP. Para obter mais informações, confira [Regras de firewall do Banco de Dados do Azure para PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída na porta 5432. O Banco de Dados do Azure para PostgreSQL usa essa porta.

[Está com problemas? Fale conosco.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Conectar-se ao servidor com psql

Use o [psql](http://postgresguide.com/utilities/psql.html) ou o [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), que são clientes populares do PostgreSQL. Neste guia de início rápido, vamos nos conectar usando o psql no [Azure Cloud Shell](../cloud-shell/overview.md) dentro do portal do Azure.

1. Anote o nome do servidor, o nome de logon do administrador do servidor, a senha e a ID da assinatura do servidor recém-criado na seção **Visão geral** do servidor.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="obter informações de conexão.":::


2. Abra o Azure Cloud Shell no portal selecionando o ícone no lado superior esquerdo.

   > [!NOTE]
   > Se você estiver abrindo o Cloud Shell pela primeira vez, verá um aviso que solicitará a criação de um grupo de recursos e uma conta de armazenamento. Essa é uma etapa única e será anexada automaticamente para todas as sessões.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Captura de tela que mostra as informações do servidor e o ícone para abrir o Azure Cloud Shell.":::

3. Execute o comando a seguir no terminal do Azure Cloud Shell. Substitua os valores pelos nomes reais do servidor e de logon do usuário administrador. Use o banco de dados vazio **postgres** com o usuário administrador neste formato: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Veja como é a experiência no terminal do Cloud Shell:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. No mesmo terminal do Azure Cloud Shell, crie um banco de dados chamado **convidado**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Alterne as conexões para o banco de dados **convidado** recém-criado.

   ```bash
   \c guest
   ```
6. Digite `\q` e selecione a tecla ENTER para fechar o psql.

[Está com problemas? Fale conosco.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpar os recursos
Você criou com êxito um servidor do Banco de Dados do Azure para PostgreSQL em um grupo de recursos. Se você não espera precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou o servidor PostgreSQL.

Para excluir o grupo de recursos:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção.
2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
3. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
4. Na caixa de diálogo de confirmação, insira o nome do seu grupo de recursos e selecione **Excluir**.

Para excluir o servidor, selecione o botão **Excluir** na página **Visão geral** do servidor:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Captura de tela que mostra o botão usado para excluir um servidor.":::

[Está com problemas? Fale conosco.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migrar seu banco de dados usando a exportação e a importação](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Criar um banco de dados](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Não foi possível encontrar o que estava procurando? Fale conosco.](https://aka.ms/postgres-doc-feedback)