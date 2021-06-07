---
title: Operações do Azure Active Directory Connect Health
description: Este artigo descreve as operações adicionais que podem ser executadas após a implantação do Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e41a00c1489d5b7b4822fec8bb4da35e917826
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686454"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operações do Azure Active Directory Connect Health
Este tópico descreve as várias operações que você pode executar usando o Azure AD (Azure Active Directory) Connect Health.

## <a name="enable-email-notifications"></a>Habilitar notificações por email
Você pode configurar o serviço do Azure AD Connect Health para enviar notificações por email quando alertas indicarem que sua infraestrutura de identidade não estiver íntegra. Isso ocorre quando um alerta é gerado e quando ele é resolvido.

![Captura de tela das configurações de notificação por email do Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> As notificações por email são habilitadas por padrão.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para habilitar as notificações por email do Azure AD Connect Health
1. Abra a folha **Alertas** do serviço para o qual deseja receber notificação por email.
2. Na barra de ação, clique em **Configurações de Notificação**.
3. Na opção de notificação por email, selecione **ON**.
4. Selecione a caixa de seleção se desejar que todos os administradores globais recebam notificações por email.
5. Se você deseja receber notificações por email em qualquer outro endereço de email, especifique-os na caixa **Destinatários de Email Adicionais**. Para remover um endereço de email da lista, clique com o botão direito do mouse na entrada e selecione **Excluir**.
6. Para finalizar as alterações, clique em **Salvar**. As alterações entram em vigor somente depois de salvar.

>[!NOTE] 
> Quando há problemas ao processar solicitações de sincronização em nosso serviço de back-end, esse serviço envia um email de notificação com os detalhes do erro para os endereços de email de contato administrativo do seu locatário. Ouvimos comentários de clientes que, em determinados casos, o volume dessas mensagens é extremamente grande, portanto, estamos alterando a maneira como enviamos essas mensagens. 
>
> Em vez de enviar uma mensagem para cada erro de sincronização toda vez que ocorrer, enviaremos um resumo diário de todos os erros retornados pelo serviço de back-end. Isso permite que os clientes processem esses erros de maneira mais eficiente e reduzam o número de mensagens de erro duplicadas.

## <a name="delete-a-server-or-service-instance"></a>Excluir uma instância de serviço ou servidor

>[!NOTE] 
> Uma licença do Microsoft Azure AD Premium é necessária para as etapas de exclusão.

Em alguns casos, você poderá remover um servidor que está sendo monitorado. Aqui está o que você precisa saber para remover um servidor do serviço do Azure AD Connect Health.

Ao excluir um servidor, lembre-se do seguinte:

* Essa ação interromperá a coleta de dados desse servidor. Este servidor será removido do serviço de monitoramento. Depois dessa ação, não será possível exibir novos dados de alertas, de monitoramento ou de análise de uso desse servidor.
* Esta ação não desinstala o Agente de Integridade do servidor. Se você não tiver desinstalado o Agente de Integridade antes de executar essa etapa, poderão aparecer erros relacionados ao Agente de Integridade no servidor.
* Essa ação não exclui os dados já coletados neste servidor. Esses dados são excluídos de acordo com a política de retenção de dados do Azure.
* Depois de executar esta ação, se desejar começar a monitorar o mesmo servidor novamente, desinstale e reinstale o Agente de Integridade nesse servidor.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Excluir um servidor do serviço do Azure AD Connect Health

>[!NOTE] 
> Uma licença do Microsoft Azure AD Premium é necessária para as etapas de exclusão.

Azure AD Connect Health para Serviços de Federação do Active Directory (AD FS) e Azure AD Connect (sincronização):

1. Abra a folha **Servidor** da folha **Lista de Servidores**, selecionando o nome do servidor a ser removido.
2. Na folha **Servidor**, na barra de ações, clique em **Excluir**.
![Captura de tela do servidor de exclusão do Azure Active Directory Connect Health](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Confirme digitando o nome do servidor na caixa de confirmação.
4. Clique em **Excluir**.

Azure AD Connect Health para Azure Active Directory Domain Services:

1. Abra o painel **Controladores de Domínio**.
2. Selecione o controlador de domínio a ser removido.
3. Na barra de ações, clique em **Excluir Selecionado**.
4. Confirme a ação para excluir o servidor.
5. Clique em **Excluir**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Excluir uma instância de serviço do serviço do Azure AD Connect Health
Em alguns casos, convém remover uma instância de serviço. Aqui está o que você precisa saber para remover uma instância de serviço do serviço do Azure AD Connect Health.

Ao excluir uma instância de serviço, esteja ciente do seguinte:

* Esta ação remove a instância de serviço atual do serviço de monitoramento.
* Essa ação não desinstala ou remove o Agente de Integridade de nenhum um dos servidores monitorados como parte desta instância de serviço. Se você não tiver desinstalado o Agente de Integridade antes de executar essa etapa, poderão aparecer erros relacionados ao Agente de Integridade nos servidores.
* Todos os dados dessa instância do serviço são excluídos de acordo com a política de retenção de dados do Azure.
* Depois de executar esta ação, se você desejar iniciar o serviço de monitoramento, desinstale e reinstale o Agente de Integridade em todos os servidores. Depois de executar esta ação, se você desejar iniciar o monitoramento do mesmo servidor novamente, desinstale, reinstale e registre o Agente de Integridade nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para excluir uma instância de serviço do serviço do Azure AD Connect Health
1. Abra a folha **Serviço** na folha **Lista de Serviços**, selecionando o identificador de serviço (nome do farm) que você deseja remover. 
2. Na folha **Serviço**, na barra de ações, clique em **Excluir**. 
![Captura de tela do serviço de exclusão do Azure Active Directory Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confirme digitando o nome do serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Excluir**.
   <br><br>

[//]: # (Início da seção de RBAC)
## <a name="manage-access-with-azure-rbac"></a>Gerenciar o acesso com o RBAC do Azure
[ O Controle de acesso baseado em função (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) para Azure AD Connect Health fornece acesso a usuários e grupos diferentes de administradores globais. O Azure RBAC atribui funções aos usuários e grupos pretendidos e fornece um mecanismo para limitar os administradores globais em seu diretório.

### <a name="roles"></a>Funções
O Azure AD Connect Health dá suporte às seguintes funções internas:

| Função | Permissões |
| --- | --- |
| Proprietário |Os proprietários podem *gerenciar o acesso* (por exemplo, atribuir uma função a um usuário ou grupo), *exibir todas as informações* (por exemplo, exibir alertas) no portal e *alterar configurações* (por exemplo, as notificações por email) no Azure AD Connect Health. <br>Por padrão, os administradores globais do Azure AD são atribuídos a essa função e isso não pode ser alterado. |
| Colaborador |Os colaboradores podem *exibir todas as informações* (por exemplo, exibir alertas) no portal e *alterar as configurações* (por exemplo, notificações por email) no Azure AD Connect Health. |
| Leitor |Os leitores podem *exibir todas as informações* (por exemplo, exibir alertas) no portal no Azure AD Connect Health. |

Todas as outras funções (como administradores de acesso de usuário ou usuários do DevTest Labs) não têm impacto de acesso no Azure AD Connect Health, mesmo se as funções estiverem disponíveis na experiência do portal.

### <a name="access-scope"></a>Escopo de acesso
O Azure AD Connect Health dá suporte ao gerenciamento de acesso em dois níveis:

* **Todas as instâncias de serviço**: esse é o caminho recomendado na maioria dos casos. Ele controla o acesso para todas as instâncias de serviço (por exemplo, um farm do AD FS) em todos os tipos de função que estão sendo monitorados pelo Azure AD Connect Health.
* **Instância de serviço**: em alguns casos, talvez seja necessário separar o acesso baseado em tipos de função ou por uma instância de serviço. Nesse caso, você pode gerenciar o acesso no nível da instância de serviço.  

A permissão será concedida se o usuário final tiver acesso no nível do diretório ou da instância de serviço.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir que usuários ou grupos acessem o Azure AD Connect Health
As etapas a seguir mostram como permitir acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Etapa 1: Selecionar o escopo de acesso apropriado
Para permitir a um usuário o acesso no nível *Todas as instâncias de serviço* no Azure AD Connect Health, abra a folha principal no Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Etapa 2: Adicionar usuários e grupos e atribuir funções
1. Na seção **Configurar**, clique em **Usuários**.<br>
   ![Captura de tela da barra lateral de recursos  do Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecione **Adicionar**.
3. No painel **Selecionar uma função**, selecione uma função (por exemplo, **Proprietário**).<br>
   ![Captura de tela do Azure AD Connect Health e menu Configurar do Azure RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Digite o nome ou identificador do usuário ou grupo de destino. Você pode selecionar um ou mais usuários ou grupos ao mesmo tempo. Clique em **Selecionar**.
   ![Captura de tela de Azure AD Connect Health e lista de funções do Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Após a conclusão da atribuição de função, os usuários e grupos aparecerão na lista.<br>
   ![Captura de tela do Azure AD Connect Health, do Azure RBAC e de novos usuários realçados](./media/how-to-connect-health-operations/RBAC_user_list.png)

Agora os usuários e grupos listados têm acesso, de acordo com suas funções atribuídas.

> [!NOTE]
> * Os administradores globais sempre têm acesso completo a todas as operações, mas as contas de administrador global não estão presentes na lista anterior.
> * Não há suporte para o recurso Convidar Usuários no Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Etapa 3: Compartilhe o local da folha com usuários ou grupos
1. Depois que você atribuir permissões, o usuário poderá acessar o Azure AD Connect Health [aqui](https://aka.ms/aadconnecthealth).
2. Na folha, o usuário pode fixar a folha ou diferentes partes dela no painel. Basta clicar no ícone **Fixar no painel**.<br>
   ![Captura de tela do Azure AD Connect Health e da folha de marcador do Azure RBAC, com ícone de marcador realçado](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Um usuário com a função Leitor atribuído não pode obter a extensão do Azure AD Connect Health no Azure Marketplace. O usuário não pode executar a operação "create" necessária para fazer isso. O usuário ainda poderá acessar a folha usando o link apresentado anteriormente. Para uso subsequente, o usuário pode fixar a folha no painel.
>
>

### <a name="remove-users-or-groups"></a>Remover usuários ou grupos
Você pode remover um usuário ou um grupo adicionado ao Azure AD Connect Health e ao Azure RBAC. Basta clicar com o botão direito do mouse no usuário ou grupo e selecionar **Remover**.<br>
![Captura de tela do Azure AD Connect Health e do Azure RBAC com a opção Remover realçada](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fim da seção de RBAC)

## <a name="next-steps"></a>Próximas etapas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Usando o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](reference-connect-health-version-history.md)
