---
title: Gerenciar regras de firewall-portal do Azure-banco de dados do Azure para MySQL
description: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540477"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um servidor de banco de dados do Azure para MySQL a partir de um endereço IP especificado ou de um intervalo de endereços IP. 

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

1. Na página do servidor do MySQL, na seção Configurações, clique em **Segurança da Conexão** para acessar a página Segurança da Conexão do Banco de Dados do Azure para MySQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="portal do Azure clique em segurança de conexão":::

2. Clique em **Adicionar Meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como visto pelo sistema do Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Portal do Azure - clique em Adicionar Meu IP":::

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.

   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "qual é meu endereço IP".

4. Adicionar outros intervalos de endereço. Nas regras do firewall de Banco de Dados do Azure para MySQL, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra para um único endereço IP, digite o mesmo endereço nos campos IP inicial e IP Final. Abrir o firewall permite que os administradores, usuários e aplicativos acessem qualquer banco de dados no servidor MySQL ao qual eles têm credenciais válidas.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Regras de portal do Azure firewall":::

5. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Portal do Azure - clique em Salvar":::

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que aplicativos do Azure se conectem ao seu Banco de Dados do Azure para servidor MySQL, as conexões do Azure deverão estar habilitadas. Por exemplo, para hospedar um aplicativo dos Aplicativos Web do Azure, um aplicativo executando em uma VM do Azure ou se conectar a partir de um gateway de gerenciamento de dados do Azure Data Factory. Os recursos não precisam estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall habilitar essas conexões. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Há alguns métodos para habilitar esses tipos de conexões. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o Banco de Dados do Azure para servidor MySQL.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor usando o Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique em **+ Adicionar Meu IP**. Clique em **Salvar** para salvar as alterações.
* Para adicionar mais endereços IP, digite o **NOME DA REGRA**, o **IP INICIAL** e o **IP FINAL**. Clique em **Salvar** para salvar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique-a. Clique em **Salvar** para salvar as alterações.
* Para excluir uma regra existente, clique nas reticências […] e, em seguida, clique em **Excluir**. Clique em **Salvar** para salvar as alterações.


## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível gerar um script para [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando a CLI do Azure](howto-manage-firewall-using-cli.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para MySQL, consulte [bibliotecas de conexões do banco de dados do Azure para MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
