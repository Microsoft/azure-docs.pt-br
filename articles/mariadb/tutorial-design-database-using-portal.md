---
title: 'Tutorial: Criar um Banco de Dados do Azure para MariaDB – portal do Azure'
description: Este tutorial explica como criar e gerenciar um servidor do Banco de Dados do Azure para MariaDB usando o portal do Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664462"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Tutorial: Projetar um Banco de Dados do Azure para MariaDB usando o portal do Azure

O Banco de Dados do Azure para MariaDB é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados do MySQL altamente disponíveis na nuvem. Usando o portal do Azure, você pode gerenciar facilmente seu servidor e projetar um banco de dados.

Neste tutorial, você usará o Portal do Azure para aprender a:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para MariaDB
> * Configurar o firewall do servidor
> * Usar a ferramenta de linha de comando do MySQL para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

No navegador, acesse o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um servidor do Banco de Dados do Azure para MariaDB

Crie um servidor do Banco de Dados do Azure para MariaDB com um conjunto definido de [recursos de computação e armazenamento](concepts-pricing-tiers.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Marque o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MariaDB**. Digite também **MariaDB** na caixa de pesquisa para encontrar o serviço.

   ![Acessar o MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Selecione o bloco **Banco de Dados do Azure para MariaDB**. Insira ou selecione as informações necessárias.

   ![Criar formulário](./media/tutorial-design-database-using-portal/2-create-form.png)

    Configuração | Valor sugerido | Descrição do campo
    ---|---|---
    Nome do servidor | *um nome de servidor exclusivo* | Escolha um nome exclusivo que identifique o servidor de Banco de Dados do Azure para MariaDB. Por exemplo, **mydemoserver**. O nome de domínio *.mariadb.database.azure.com* é acrescentado ao nome do servidor que você inseriu. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter entre 3 e 63 caracteres.
    Subscription | *sua assinatura* | Selecione a assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a assinatura na qual você recebe a cobrança do recurso.
    Resource group | **myresourcegroup** | Insira o nome de um novo grupo de recursos ou escolha um grupo de recursos existente.
    Selecionar a origem | **Em branco** | Selecione **Em branco** para criar um novo servidor. (Selecione **Backup** caso esteja criando um servidor a partir de um backup de área geográfica de um servidor de Banco de Dados do Azure para MariaDB existente.)
    Logon de administrador do servidor | **myadmin** | A conta de entrada para usar ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
    Senha | *sua escolha* | Insira uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
    Confirmar senha | *sua escolha*| Confirme a senha da conta do administrador.
    Location | *a região mais próxima de seus usuários*| Selecione o local mais próximo de seus usuários ou de outros aplicativos do Azure.
    Versão | *a versão mais recente*| A versão mais recente (a menos que você tenha requisitos específicos para usar uma versão diferente).
    Tipo de preço | Confira a descrição. | As configurações de computação, armazenamento e backup para o novo servidor. Escolha **Tipo de preço** > **Uso Geral**. Mantenha os valores padrão para as seguintes configurações:<br><ul><li>**Geração de Computação** (Gen 5)</li><li>**vCore** (4 vCores)</li><li>**Armazenamento** (100 GB)</li><li>**Período de Retenção de Backup** (7 dias)</li></ul><br>Para habilitar os backups do servidor em armazenamento com redundância geográfica, escolha **Redundância Geográfica** nas **Opções de Redundância de Backup**. <br><br>Para salvar a seleção desse tipo de preço, selecione **OK**. A captura de tela a seguir demonstra essas seleções.
    
   ![Tipo de preço](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > Com o **aumento automático** habilitado, seu servidor aumenta o armazenamento quando você está se aproximando do limite alocado, sem afetar sua carga de trabalho.

4. Clique em **Revisar + Criar**. Na barra de ferramentas, clique no botão **Notificações** para monitorar o processo de implantação. A implantação pode levar até 20 minutos.

## <a name="configure-the-firewall"></a>Configurar o firewall

Um Banco de Dados do Azure para MariaDB é protegido por um firewall. Por padrão, todas as conexões ao servidor e aos bancos de dados dentro do servidor são rejeitadas. Antes de se conectar ao Banco de Dados do Azure para MariaDB pela primeira vez, configure o firewall para adicionar o endereço IP (ou intervalo de endereços IP) da rede pública do computador cliente.

1. Selecione seu servidor recém-criado, depois selecione **Segurança de conexão**.

   ![Segurança da conexão](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Você pode selecionar **Adicionar Meu IP** ou configurar regras de firewall aqui. Lembre-se de selecionar **Salvar** depois de criar as regras.

Agora você pode se conectar ao servidor usando a ferramenta de linha de comando do MySQL ou MySQL Workbench.

> [!TIP]
> O servidor do Banco de Dados do Azure para MariaDB se comunica pela porta 3306. Se estiver tentando se conectar de dentro de uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido pelo firewall da rede. Nesse caso, para se conectar ao servidor do Banco de Dados do Azure para MariaDB, seu departamento de TI deve abrir a porta 3306.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha os valores para **Nome do servidor** (totalmente qualificado) e o **Nome de logon do administrador do servidor** para o servidor do Banco de Dados do Azure para MariaDB no portal do Azure. Use o nome do servidor totalmente qualificado para se conectar ao servidor usando a ferramenta de linha de comando do MySQL.

1. No [portal do Azure](https://portal.azure.com/), no menu à esquerda, selecione **Todos os recursos**. Insira o nome do servidor e busque o servidor do Banco de Dados do Azure para MariaDB. Selecione o nome do servidor para exibir os detalhes do servidor.

2. Na página **Visão Geral**, anote os valores do **Nome do servidor** e do **Nome de logon do administrador do servidor**. Você também pode selecionar o botão **Copiar** ao lado de cada campo para copiar o valor para a área de transferência.

   ![Propriedades do servidor](./media/tutorial-design-database-using-portal/2-server-properties.png)

Em nosso exemplo, o nome do servidor é **mydemoserver.mariadb.database.azure.com** e o nome de logon do administrador do servidor é **myadmin\@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Conectar-se ao servidor usando o MySQL

Use a [ferramenta de linha de comando do MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma conexão com seu servidor do Banco de Dados do Azure para MariaDB. Você pode executar a ferramenta de linha de comando do MySQL no Azure Cloud Shell no navegador ou no computador usando as ferramentas do MySQL instaladas localmente. Para abrir o Azure Cloud Shell, selecione o botão **Experimentar** em um bloco de código deste artigo ou visite o portal do Azure e clique no ícone **>_** na barra de ferramentas superior direita.

Insira o comando para se conectar:

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Criar um banco de dados vazio

Quando você estiver conectado ao servidor, crie um banco de dados em branco com o qual você trabalhará:

```sql
CREATE DATABASE mysampledb;
```

No prompt, execute o seguinte comando para mudar a conexão para seu banco de dados recém-criado:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados

Agora que você sabe como se conectar ao Banco de Dados do Azure para MariaDB, é possível concluir algumas tarefas básicas.

Primeiro, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Carregar dados nas tabelas

Agora que você tem uma tabela, insira alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas

Execute a seguinte consulta para recuperar as informações da tabela do banco de dados:

```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados nas tabelas:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada ao recuperar dados:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo

Imagine que você excluiu acidentalmente uma tabela de banco de dados importante e não consegue recuperar os dados com facilidade. No Banco de Dados do Azure para MariaDB, é possível restaurar o servidor para um ponto no tempo criando uma cópia dos bancos de dados no novo servidor. Use esse novo servidor para recuperar seus dados excluídos. As etapas a seguir restauram o servidor de exemplo para um ponto no tempo anterior à adição da tabela:

1. No portal do Azure, localize o Banco de Dados do Azure para MariaDB. Na página **Visão geral**, selecione **Restaurar**.

   ![Restaurar um banco de dados](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Na página **Restaurar**, insira ou selecione as seguintes informações:

   ![Formulário de restauração](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Ponto de restauração**: selecione um ponto no tempo que você deseja restaurar, dentro do período listado. Lembre-se de converter o fuso horário local para UTC.
   - **Restaurar em novo servidor**: insira um novo nome do servidor para onde será feita a restauração.
   - **Localização**: a região é a mesma do servidor de origem e não pode ser alterada.
   - **Tipo de preço**: o tipo de preço é o mesmo do servidor de origem e não pode ser alterado.
   
3. Clique em **OK** para [restaurar o servidor para um ponto no tempo](./howto-restore-server-portal.md) anterior à exclusão da tabela. A restauração de um servidor cria uma nova cópia do servidor no ponto no tempo selecionado. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usará o Portal do Azure para aprender a:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para MariaDB
> * Configurar o firewall do servidor
> * Usar a ferramenta de linha de comando do MySQL para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

> [!div class="nextstepaction"]
> [Como conectar aplicativos ao Banco de Dados do Azure para MariaDB](howto-connection-string.md)
