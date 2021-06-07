---
title: 'Início Rápido: Projetar um servidor – Portal do Azure – Banco de Dados do Azure para MySQL'
description: Este artigo o orienta a usar o portal do Azure para criar rapidamente um servidor de Banco de Dados do Azure para MySQL de exemplo em aproximadamente cinco minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: b8f3a8e06bb27051417205c4dc3141948960bb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567797"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Início Rápido: Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL é um serviço gerenciado usado para executar, gerenciar e dimensionar Bancos de Dados MySQL altamente disponíveis na nuvem. Este início rápido mostra como usar o portal do Azure para criar um servidor individual para o Banco de Dados do Azure para MySQL. Ele também mostra como se conectar ao servidor.

## <a name="prerequisites"></a>Pré-requisitos
Será necessário ter uma assinatura ativa do Azure. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Criar um servidor individual para o Banco de Dados do Azure para MySQL
1. Vá para o [portal do Azure](https://portal.azure.com/) a fim de criar um banco de dados de Servidor Individual para MySQL. Pesquise e selecione o **Banco de Dados do Azure para MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Localizar o Banco de Dados do Azure para MySQL":::

1. Selecione **Adicionar**.

2. Na página **Selecionar a opção de implantação do Banco de Dados do Azure para MySQL**, selecione **Servidor individual**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Captura de tela que mostra a opção Servidor individual.":::

3. Insira as configurações básicas para um novo servidor individual:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Captura de tela que mostra a página Criar servidor MySQL.":::

   **Configuração** | **Valor sugerido** | **Descrição**
   ---|---|---
   Subscription | Sua assinatura | Selecione a Assinatura do Azure desejada.
   Resource group | **myresourcegroup** | Insira um grupo de recursos novo ou existente da sua assinatura.
   Nome do servidor | **mydemoserver** | Insira um nome exclusivo. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa conter de 3 a 63 caracteres.
   Fonte de dados |**Nenhuma** | Selecione **Nenhum** para criar um novo servidor do zero. Selecione **Backup** somente se você estiver restaurando de um backup geográfico de um servidor existente.
   Location |A localização desejada | Selecione uma localização na lista.
   Versão | A última versão principal| Use a última versão principal. Confira [todas as versões compatíveis](concepts-supported-versions.md).
   Computação + armazenamento | Usar os padrões| O tipo de preço padrão é **Uso Geral** com **4 vCores** e armazenamento de **100 GB**. A retenção de backup é definida como **7 dias**, com a opção de backup **Geograficamente Redundante**.<br/>Examine a página de [preços](https://azure.microsoft.com/pricing/details/mysql/) e atualize os padrões, se necessário.
   Nome de usuário do administrador | **mydemoadmin** | Insira o nome de usuário administrador do servidor. Não é possível usar os termos **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public** como nome de usuário administrador.
   Senha | Uma senha | Uma nova senha para o usuário administrador do servidor. A senha deve ter de 8 a 128 caracteres de comprimento com uma combinação de letras maiúsculas ou minúsculas, números e caracteres não alfanuméricos (!, $, #, % e assim por diante).
  

   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que os servidores criados no tipo de preço Básico não podem ser escalados posteriormente para Uso Geral ou Otimizado para Memória.

4. Selecione **Examinar + criar** para provisionar o servidor.

5. Aguarde até que a página do portal exiba **Sua implantação está concluída**. Selecione **Ir para o recurso** a fim de acessar a página de servidor recém-criada:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Captura de tela que mostra a mensagem A implantação está concluída.":::

[Está tendo problemas? Fale conosco.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Por padrão, o novo servidor é protegido com um firewall. Para se conectar, você deve fornecer acesso ao IP executando estas etapas:

1. Acesse **Segurança de conexão**, no painel esquerdo do recurso de servidor. Se você não souber como encontrar o recurso, confira [Como abrir um recurso](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Captura de tela que mostra a página Segurança da conexão > Regra de firewall.":::

2. Selecione **Adicionar o endereço IP do cliente atual** e escolha **Salvar**.

   > [!NOTE]
   > Para evitar problemas de conectividade, verifique se a rede permite o tráfego de saída pela porta 3306, que é usada pelo Banco de Dados do Azure para MySQL. 

Você pode adicionar mais IPs ou fornecer um intervalo de IP para se conectar ao servidor por meio desses IPs. Para obter mais informações, confira [Como gerenciar regras de firewall em um servidor do Banco de Dados do Azure para MySQL](./concepts-firewall-rules.md).


[Está tendo problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Conecte-se ao servidor usando o mysql.exe
Você pode usar o [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou o [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do ambiente local. Neste início rápido, usaremos o mysql.exe no [Azure Cloud Shell](../cloud-shell/overview.md) para se conectar ao servidor.


1. Abra o Azure Cloud Shell no portal selecionando o primeiro botão da barra de ferramentas, conforme mostrado na captura de tela a seguir. Anote o nome do servidor, o nome do administrador do servidor e a assinatura do novo servidor na seção **Visão Geral**, conforme mostrado na captura de tela.

    > [!NOTE]
    > Se você estiver abrindo o Cloud Shell pela primeira vez, será solicitado que crie um grupo de recursos e uma conta de armazenamento. Essa é uma etapa que só precisa ser realizada uma vez. Ela será anexada automaticamente para todas as sessões.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Captura de tela que mostra o Cloud Shell no portal do Azure.":::
2. Execute o comando a seguir no terminal do Azure Cloud Shell. Substitua os valores mostrados aqui pelos nomes reais do servidor e de usuário administrador. Para o Banco de Dados do Azure para MySQL, você precisa adicionar `@\<servername>` ao nome de usuário administrador, como mostrado aqui: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Veja como ficará a aparência dele no terminal do Cloud Shell:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. No mesmo terminal do Azure Cloud Shell, crie um banco de dados chamado `guest`:
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Alterne para o banco de dados `guest`:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Digite `quit` e clique em **Enter** para encerrar o MySQL.

[Está tendo problemas? Fale conosco.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Limpar recursos
Você criou um servidor de Banco de Dados do Azure para MySQL em um grupo de recursos.  Caso não espere precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou exclua apenas o servidor MySQL. Para excluir o grupo de recursos, conclua estas etapas:
1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção.
2. Na lista de grupos de recursos, selecione o nome do grupo de recursos.
3. Na página **Visão Geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

Para excluir o servidor, você pode selecionar **Excluir** na página **Visão Geral** do servidor, conforme mostrado aqui:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Captura de tela que mostra o botão Excluir na página Visão Geral do servidor.":::

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
>[Criar um aplicativo PHP no Windows com o MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Criar aplicativo PHP no Linux com o MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Não conseguiu encontrar o que estava procurando? Fale conosco.](https://aka.ms/mysql-doc-feedback)
