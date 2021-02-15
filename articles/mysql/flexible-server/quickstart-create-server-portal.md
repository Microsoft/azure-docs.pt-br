---
title: 'Início Rápido: Criar um servidor flexível do Banco de Dados do Azure para MySQL – portal do Azure'
description: Este artigo explica como usar o portal do Azure para criar um servidor flexível do Banco de Dados do Azure para MySQL em minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566751"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Início Rápido: Usar o portal do Azure para criar um servidor flexível do Banco de Dados do Azure para MySQL

O servidor flexível do Banco de Dados do Azure para MySQL é um serviço gerenciado que você pode usar para executar, gerenciar e escalar servidores MySQL altamente disponíveis na nuvem. Este guia de início rápido mostra como criar um servidor flexível usando o portal do Azure.

> [!IMPORTANT] 
> Atualmente, o servidor flexível do Banco de Dados do Azure para MySQL está em versão prévia pública.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Vá para o [Portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar um servidor flexível do Banco de Dados do Azure para MySQL

Crie um servidor flexível com um conjunto definido de [recursos de computação e armazenamento](./concepts-compute-storage.md). Crie o serviço dentro de um [Grupo de recursos do Azure](../../azure-resource-manager/management/overview.md).

Conclua estas etapas para criar um servidor flexível:

1. Pesquise e selecione **Servidores do Banco de Dados do Azure para MySQL** no portal:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Captura de tela que mostra uma pesquisa dos servidores do Banco de Dados do Azure para MySQL.":::

2. Selecione **Adicionar**. 

3. Na página **Selecionar opção de implantação do Banco de Dados do Azure para MySQL**, escolha **Servidor flexível** como a opção de implantação:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Captura de tela que mostra a opção Servidor flexível.":::    

4. Na guia **Noções básicas**, insira as seguintes informações: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Captura de tela que mostra a guia Informações Básicas da página Servidor flexível."::: 
                                    
    |**Configuração**|**Valor sugerido**|**Descrição**|
    |---|---|---|
    Subscription|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Caso você tenha várias assinaturas, escolha a assinatura na qual deseja receber a cobrança do recurso.|
    Resource group|**myresourcegroup**| Um novo nome do grupo de recursos ou um existente de sua assinatura.|
    Nome do servidor |**mydemoserver**|Um nome exclusivo que identifique o servidor flexível. O nome de domínio `mysql.database.azure.com` é acrescentado ao nome do servidor fornecido. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter entre 3 e 63 caracteres.|
    Nome de usuário do administrador |**mydemouser**| Sua conta de entrada para usar ao se conectar ao servidor. O nome de usuário administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.|
    Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. Ela também precisa conter caracteres de três das seguintes categorias: letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).|
    Região|A região mais próxima dos usuários| A localização mais próxima dos usuários.|
    Versão|**5.7**| Uma versão principal do MySQL.|
    Computação + armazenamento | **Expansível**, **Standard_B1ms**, **10 GiB**, **7 dias** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. **Com capacidade de intermitência**, **Standard_B1ms**, **10 GiB** e **7 dias** são os valores padrão de **Camada de computação**, **Tamanho da computação**, **Tamanho do armazenamento** e **Período de retenção de backup**. Você pode manter esses controles deslizantes no estado em que se encontram ou ajustá-los. Para salvar a seleção de computação e armazenamento, escolha **Salvar** para continuar com a configuração. A captura de tela a seguir mostra as opções de computação e armazenamento.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Captura de tela que mostra as opções de computação e armazenamento.":::

5. Configure as opções de rede.

    Na guia **Rede**, você pode escolher como o servidor poderá ser acessado. O servidor flexível do Banco de Dados do Azure para MySQL fornece duas maneiras de se conectar ao servidor: 
   - Acesso público (endereços IP permitidos)
   - Acesso privado (Integração VNet) 
   
   Quando você usa o acesso público, o acesso ao seu servidor fica limitado aos endereços IP permitidos que você adiciona a uma regra de firewall. Esse método impede que aplicativos e ferramentas externas se conectem ao servidor e aos bancos de dados do servidor, a menos que você crie uma regra para abrir o firewall em um endereço IP ou um intervalo específico. Quando você usa o acesso privado (Integração VNET), o acesso ao seu servidor fica limitado à sua rede virtual. Neste guia de início rápido, você aprenderá a habilitar o acesso público para se conectar ao servidor. [Saiba mais sobre os métodos de conectividade no artigo sobre conceitos.](./concepts-networking.md)

    > [!NOTE]
    > Não é possível alterar o método de conectividade após a criação do servidor. Por exemplo, se você selecionar **Acesso público (endereços IP permitidos)** ao criar o servidor, não poderá alterar a opção para **Acesso privado (Integração VNET)** depois que o servidor for criado. Recomendamos expressamente que você crie seu servidor com o acesso privado para ajudar a proteger o acesso ao servidor por meio da Integração VNET. [Saiba mais sobre o acesso privado no artigo sobre conceitos.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Captura de tela que mostra a guia Rede.":::  

6. Selecione **Examinar + criar** para examinar a configuração do servidor flexível.

7. Selecione **Criar** para provisionar o servidor. O provisionamento pode levar alguns minutos.

8. Selecione **Notificações** na barra de ferramentas (botão de sino) para monitorar o processo de implantação. Depois que a implantação for concluída, selecione **Fixar no painel**, o que criará um bloco para o servidor flexível no painel do portal do Azure. Este bloco é um atalho para a página **Visão geral** do servidor. Quando você seleciona **Ir para o recurso**, a página **Visão geral** do servidor é aberta.

Por padrão, estes bancos de dados são criados no seu servidor: information_schema, mysql, performance_schema e sys.

> [!NOTE]
> Para evitar problemas de conectividade, verifique se a rede permite o tráfego de saída na porta 3306, que é usada pelo servidor flexível do Banco de Dados do Azure para MySQL.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Conecte-se ao servidor usando o mysql.exe

Se você criou o servidor flexível com o acesso privado (Integração VNET), conecte-se ao servidor por meio de um recurso na mesma rede virtual do servidor. Crie uma máquina virtual e adicione-a à rede virtual criada com o servidor flexível.

Se você criou o servidor flexível com o acesso público (endereços IP permitidos), adicione seu endereço IP local à lista de regras de firewall no servidor.

Você pode usar o [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou o [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do ambiente local. 

Se estiver usando o mysql.exe, conecte-se usando o comando a seguir. Use o nome do servidor, o nome de usuário e a senha no comando. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Limpar os recursos
Você acabou de criar um servidor flexível do Banco de Dados do Azure para MySQL em um grupo de recursos. Caso não espere precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou exclua apenas o servidor MySQL. Para excluir o grupo de recursos, conclua estas etapas:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção.
1. Na lista de grupos de recursos, selecione o nome do grupo de recursos.
1. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
1. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

Para excluir o servidor, selecione **Excluir** na página **Visão geral** do servidor, conforme mostrado aqui:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Captura de tela que mostra como excluir um servidor.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)