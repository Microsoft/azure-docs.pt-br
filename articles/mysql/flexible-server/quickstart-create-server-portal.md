---
title: 'Início Rápido: Criar um servidor flexível do Banco de Dados do Azure para MySQL – portal do Azure'
description: Este artigo explica como usar o portal do Azure para criar um servidor flexível do Banco de Dados do Azure para MySQL em minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 53878384f4eb056f0cb23ec9005043ac26c8fad2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492539"
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
    Região|A região mais próxima dos usuários| A localização mais próxima dos usuários.|
    Tipo de carga de trabalho| Desenvolvimento | Para a carga de trabalho de produção, você pode escolher tamanho pequeno/médio ou grande, dependendo dos requisitos de [max_connections](concepts-server-parameters.md#max_connections)|
    Zona de disponibilidade| Sem preferência | Se o aplicativo nas VMs do Azure, os conjuntos de dimensionamento de máquinas virtuais ou a instância AKS forem provisionados em uma zona de disponibilidade específica, você poderá especificar o servidor flexível na mesma zona de disponibilidade a fim de posicionar o aplicativo e o banco de dados de modo a melhorar o desempenho, reduzindo a latência de rede entre as zonas.|
    Alta disponibilidade| Padrão | Para servidores de produção, a habilitação da HA (alta disponibilidade) redundante na zona é altamente recomendável para a continuidade dos negócios e a proteção contra eventuais falhas de zona|
    Versão do MySQL|**5.7**| Uma versão principal do MySQL.|
    Nome de usuário do administrador |**mydemouser**| Sua conta de entrada para usar ao se conectar ao servidor. O nome de usuário administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.|
    Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. Ela também precisa conter caracteres de três das seguintes categorias: letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).|
    Computação + armazenamento | **Com capacidade de intermitência**, **Standard_B1ms**, **10 GiB**, **100 IOPS**, **7 dias** | As configurações de computação, armazenamento, IOPS e backup para o novo servidor. Selecione **Configurar servidor**. **Com capacidade de intermitência**, **Standard_B1ms**, **10 GiB**, **100 IOPS** e **7 dias** são os valores padrão de **Camada de computação**, **Tamanho da computação**, **Tamanho do armazenamento**, **IOPS** e **Período de retenção** de backup. Você pode manter esses controles deslizantes no estado em que se encontram ou ajustá-los. Para cargas de dados mais rápidas durante a migração, é recomendável aumentar a IOPS para o tamanho máximo compatível com o tamanho da computação e, posteriormente, redimensioná-la de volta para poupar custos. Para salvar a seleção de computação e armazenamento, escolha **Salvar** para continuar com a configuração. A captura de tela a seguir mostra as opções de computação e armazenamento.|
    
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

Se você criou o servidor flexível com o acesso privado (Integração VNET), conecte-se ao servidor por meio de um recurso na mesma rede virtual do servidor. Crie uma máquina virtual e adicione-a à rede virtual criada com o servidor flexível. Veja a documentação sobre como [configurar o acesso privado](how-to-manage-virtual-network-portal.md) para saber mais.

Se você criou o servidor flexível com o acesso público (endereços IP permitidos), adicione seu endereço IP local à lista de regras de firewall no servidor. Veja a [documentação sobre como criar ou gerenciar regras de firewall](how-to-manage-firewall-portal.md) para obter diretrizes passo a passo.

Você pode usar o [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou o [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do ambiente local. O Banco de Dados do Azure para MySQL Servidor Flexível dá suporte à conexão dos aplicativos clientes ao serviço do MySQL usando TLS (Protocolo TLS), anteriormente conhecido como SSL (Protocolo SSL). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos clientes, permitindo que você atenda aos requisitos de conformidade. Para se conectar com o servidor flexível MySQL, você precisará baixar o [certificado SSL público](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) para verificação da autoridade de certificação.

O exemplo a seguir mostra como se conectar ao servidor flexível usando a interface de linha de comando do MySQL. Primeiro, você instalará a linha de comando do MySQL, caso ela ainda não esteja instalada. Você baixará o certificado DigiCertGlobalRootCA exigido para conexões SSL. Use a configuração de cadeia de conexão --ssl-mode=REQUIRED para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o parâmetro --ssl-ca. Substitua os valores por um nome do servidor e uma senha que sejam reais.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se você tiver provisionado seu servidor flexível usando o **acesso público**, também poderá usar o [Azure Cloud Shell](https://shell.azure.com/bash) para se conectar ao seu servidor flexível usando o cliente mysql pré-instalado conforme mostrado abaixo:

Para usar Azure Cloud Shell para se conectar ao seu servidor flexível, você precisará permitir o acesso à rede de Azure Cloud Shell ao seu servidor flexível. Para fazer isso, você pode ir para a folha **Rede** no portal do Azure para seu servidor flexível MySQL e marcar a caixa na seção **Firewall** que diz "Permitir acesso público de qualquer serviço do Azure para este servidor", conforme mostrado na captura de tela abaixo, e clicar em Salvar para manter a configuração.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Captura de tela que mostra como permitir acesso do Azure Cloud Shell ao servidor flexível MySQL para configuração da rede de acesso público.":::

> [!NOTE]
> Marcar a caixa **Permitir acesso público de qualquer serviço do Azure no Azure para este servidor** deve ser usado somente para desenvolvimento ou teste. Isso configura o firewall para permitir conexões de endereços IP alocados para qualquer serviço ou ativo do Azure, incluindo conexões das assinaturas de outros clientes.

Clique em **Experimente** para iniciar o Azure Cloud Shell e usar os comandos a seguir para se conectar ao seu servidor flexível. Use o nome do servidor, o nome de usuário e a senha no comando. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Ao se conectar ao servidor flexível usando o Azure Cloud Shell, você precisará usar o parâmetro --ssl=true e não o --ssl-mode=REQUIRED.
> O principal motivo é que Azure Cloud Shell vem com o cliente pré-instalado mysql.exe da distribuição MariaDB, que exige o parâmetro --ssl, enquanto o cliente MySQL da distribuição da Oracle exige o parâmetro --ssl-mode.

Se você vir a mensagem de erro a seguir ao conectar-se ao seu servidor flexível seguindo o comando anterior, você perdeu a configuração da regra de firewall usando "Permitir acesso público de qualquer serviço do Azure no Azure para este servidor" mencionado anteriormente ou a opção não foi salva. Tente configurar o firewall outra vez e tente novamente.

ERRO 2002 (HY000): não é possível se conectar ao servidor MySQL em <servername> (115)

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
