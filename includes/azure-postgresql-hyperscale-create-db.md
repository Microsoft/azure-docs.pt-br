---
title: incluir arquivo
description: incluir arquivo
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 12/16/2020
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 26289f6bdfa086708548d17125a6dfdf50b02a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97665140"
---
## <a name="create-a-hyperscale-citus-server-group"></a>Criar um grupo de servidores Hyperscale (Citus)

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).


Siga estas etapas para criar um Banco de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para PostgreSQL** na página **Bancos de Dados**.
3. Para a opção de implantação, clique no botão **Criar** em **grupo de servidores Hyperscale (Citus).**
4. Preencha o formulário de detalhes sobre o novo servidor com as seguintes informações:
   - Grupo de recursos: clique no link **Criar novo** abaixo da caixa de texto desse campo. Insira um nome como **myresourcegroup**.
   - Nome do grupo de servidores: digite um nome exclusivo para o novo grupo de servidores que também poderá ser usado para um subdomínio do servidor.
   - Nome de usuário do administrador: no momento deve ser o valor **citus** e não pode ser alterado.
   - Senha: deve ter pelo menos oito caracteres de comprimento e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 – 9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
   - Localização: use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A senha de administrador do servidor que você especificar aqui é necessária para fazer logon no servidor e nos bancos de dados. Lembre-se ou registre essas informações para o uso posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações nessa seção inalteradas e clique em **Salvar**.
6. Clique em **Próximo: Rede >** na parte inferior da tela.

7. Na guia **Rede**, clique no botão de opção **Ponto de extremidade público**.
   ![Ponto de extremidade público selecionado](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Clique no link **+ Adicionar endereço IP do cliente atual**.
   ![IP do cliente adicionado](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não poderá se conectar ao cluster do Hiperescala (Citus), a menos que o departamento de TI abra a porta 5432.
   >

9. Clique em **Examinar + criar** e, em seguida, **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.
10. A página será redirecionada para monitorar a implantação. Quando o status em tempo real mudar de **Sua implantação está em andamento** para **Sua implantação está concluída**, clique no item de menu **Saídas** no lado esquerdo da página.
11. A página de saídas conterá um nome do host do coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para uso posterior.

### <a name="connect-to-the-database-using-psql"></a>Conectar-se ao banco de dados usando psql

Ao criar o servidor do Banco de Dados do Azure para PostgreSQL, um banco de dados padrão chamado **citus** é criado. Para se conectar ao seu servidor de banco de dados, é necessário uma cadeia de conexão e a senha de administrador.

1. Obter a cadeia de conexão. Na página de grupo de servidor, clique no item de menu **Cadeias de conexão**. (Está em **Configurações**.) Localize a cadeia de caracteres marcada como **psql**. Ela terá o formato:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Copie a cadeia de caracteres. Você precisará substituir "{sua\_senha}" pela senha administrativa escolhida anteriormente. O sistema não armazena a senha de texto sem formatação e, portanto, não é possível exibi-la na cadeia de conexão.

2. Abra uma janela de terminal no computador local.

3. No prompt, conecte-se ao servidor do Banco de Dados do Azure para PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/current/app-psql.html). Passe a cadeia de conexão entre aspas, certificando-se de que contém a senha:
   ```bash
   psql "host=..."
   ```

   Por exemplo, o comando a seguir conecta-se ao nó coordenador do grupo de servidores **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
