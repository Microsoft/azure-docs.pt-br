---
title: Criar e gerenciar regras de firewall do MariaDB no Banco de Dados do Azure para MariaDB
description: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e9ab243692f5a4a1ec7de25774f5bad867698fc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746062"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure
Regras de firewall de nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados do Azure para servidor MariaDB de um endereço IP especificado ou um intervalo de endereços IP.

Regras de VNet (rede) virtuais também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre [criando e gerenciando a rede Virtual regras usando o portal do Azure e pontos de extremidade de serviço](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

1. Na página do servidor do MariaDB, na seção Configurações, clique em **Segurança da Conexão** para acessar a página Segurança da Conexão do Banco de Dados do Azure para MariaDB.

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Clique em **Adicionar Meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como visto pelo sistema do Azure.

   ![Portal do Azure - clique em Adicionar Meu IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.

   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "qual é meu endereço IP".

4. Adicionar outros intervalos de endereço. Nas regras do firewall de Banco de Dados do Azure para MariaDB, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra para um único endereço IP, digite o mesmo endereço nos campos IP inicial e IP Final. Abrir o firewall permite que os administradores, usuários e aplicativos acessem qualquer banco de dados no servidor MariaDB ao qual eles têm credenciais válidas.

   ![Portal do Azure - regras de firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.

   ![Portal do Azure - clique em Salvar](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que aplicativos do Azure se conectem ao seu servidor de Banco de Dados do Azure para MariaDB, as conexões do Azure deverão estar habilitadas. Por exemplo, para hospedar um aplicativo dos Aplicativos Web do Azure, um aplicativo executando em uma VM do Azure ou se conectar a partir de um gateway de gerenciamento de dados do Azure Data Factory. Os recursos não precisam estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall habilitar essas conexões. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Há alguns métodos para habilitar esses tipos de conexões. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Como alternativa, é possível configurar a opção **Permitir o Acesso aos Serviços do Azure** como **ATIVADO** no painel **Segurança da Conexão** do portal e clicar em **Salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de Banco de Dados do Azure para MariaDB.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Gerenciar as regras de firewall existentes no portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique em **+ Adicionar Meu IP**. Clique em **Salvar** para salvar as alterações.
* Para adicionar mais endereços IP, digite o **NOME DA REGRA**, o **IP INICIAL** e o **IP FINAL**. Clique em **Salvar** para salvar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique-a. Clique em **Salvar** para salvar as alterações.
* Para excluir uma regra existente, clique nas reticências […] e, em seguida, clique em **Excluir**. Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
 - Da mesma forma, você pode gerar um script para [criar e gerenciar o banco de dados do Azure para MariaDB as regras de firewall usando a CLI do Azure](howto-manage-firewall-cli.md).
 - Proteger ainda mais o acesso ao seu servidor pelo [criando e gerenciando a rede Virtual regras usando o portal do Azure e pontos de extremidade de serviço](howto-manage-vnet-portal.md).