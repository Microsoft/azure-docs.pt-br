---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172165"
---
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Criar um Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)

Siga estas etapas para criar um Banco de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para PostgreSQL** na página **Bancos de Dados**.
3. Para a opção de implantação, clique no botão **Criar** em **grupo de servidores Hyperscale (Citus) - VISUALIZAÇÃO.**
4. Preencha o formulário de detalhes sobre o novo servidor com as seguintes informações:
   - Grupo de recursos: clique no link **Criar novo** abaixo da caixa de texto desse campo. Insira um nome como **myresourcegroup**.
   - Nome do grupo de servidores: digite um nome exclusivo para o novo grupo de servidores que também poderá ser usado para um subdomínio do servidor.
   - Nome de usuário do administrador: no momento deve ser o valor **citus** e não pode ser alterado.
   - Senha: deve ter pelo menos oito caracteres de comprimento e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 – 9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
   - Localização: use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A senha de administrador do servidor que você especificar aqui é necessária para fazer logon no servidor e nos bancos de dados. Lembre-se ou registre essas informações para o uso posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações nessa seção inalteradas e clique em **Salvar**.
6. Clique em **Examinar + criar** e, em seguida, **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.
7. A página será redirecionada para monitorar a implantação. Quando o status em tempo real mudar de **Sua implantação está em andamento** para **Sua implantação está concluída**, clique no item de menu **Saídas** no lado esquerdo da página.
8. A página de saídas conterá um nome do host do coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O serviço do Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (visualização) usa um firewall no nível do servidor. Por padrão, o firewall impede que aplicativos e ferramentas externos se conectam ao nó coordenador e a qualquer banco de dados interno. É necessário adicionar uma regra para abrir o firewall para um intervalo de endereços IP específico.

1. Na seção **Saídas** em que você copiou anteriormente o nome do host do nó coordenador, clique em novamente no item de menu **Visão geral**.

2. Localize o nome do grupo de servidores da implantação e clique nele. (O nome do grupo de servidores *não* terá um sufixo. Itens com nomes que terminam em, por exemplo, "-c", "-w0" ou "-w1" não são o grupo de servidores).

3. Clique em **Firewall** em **Segurança** no menu à esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP do cliente atual**.

5. Por fim, clique no botão **Salvar**.

   > [!NOTE]
   > O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Conectar-se ao banco de dados usando psql

Ao criar o servidor do Banco de Dados do Azure para PostgreSQL, um banco de dados padrão chamado **citus** é criado. Para se conectar ao seu servidor de banco de dados, é necessário uma cadeia de conexão e a senha de administrador.

1. Obter a cadeia de conexão. Na página de grupo de servidor, clique no item de menu **Cadeias de conexão**. (Ele está em **Configurações**). Localize a cadeia de caracteres marcada **C++ (libpq)** . Ela terá o formato:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copie a cadeia de caracteres. Você precisará substituir "{sua\_senha}" pela senha administrativa escolhida anteriormente. O sistema não armazena a senha de texto sem formatação e, portanto, não é possível exibi-la na cadeia de conexão.

2. Abra uma janela de terminal no computador local.

3. No prompt, conecte-se ao servidor do Banco de Dados do Azure para PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/current/app-psql.html). Passe a cadeia de conexão entre aspas, certificando-se de que contém a senha:
   ```bash
   psql "{connection_string}"
   ```

   Por exemplo, o comando a seguir conecta-se ao nó coordenador do grupo de servidores **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
