---
title: 'Conectores na Sincronização do Azure AD Connect: interface de usuário do Synchronization Service Manager | Microsoft Docs'
description: Entenda como usar a guia Conectores no Synchronization Service Manager para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319870"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Usando conectores com o Azure AD Connect Sync Service Manager

![Captura de tela que mostra o Service Manager de sincronização.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

A guia Conectores é usada para gerenciar todos os sistemas aos quais o mecanismo de sincronização está conectado.

## <a name="connector-actions"></a>Ações do Conector
| Ação | Comentário |
| --- | --- |
| Criar |Não use. Para se conectar a florestas adicionais do AD, use o assistente de instalação. |
| Propriedades |Usado para filtragem de domínio e de UO. |
| [Delete (excluir)](#delete) |Usado para excluir os dados no espaço do conector ou para excluir a conexão a uma floresta. |
| [Configurar perfis de execução](#configure-run-profiles) |Com exceção do domínio de filtragem, não há nada a ser configurado aqui. Você pode usar essa ação para ver os perfis de execução já configurados. |
| Executar |Usado para iniciar uma execução única de um perfil. |
| Parar |Interrompe um Conector que, atualmente, executa um perfil. |
| Exportar Conector |Não use. |
| Importar Conector |Não use. |
| Atualizar Conector |Não use. |
| Atualizar Esquema |Atualiza o esquema em cache. É preferível usar a opção no assistente de instalação, já que ela também atualiza as regras de sincronização. |
| [Pesquisar Espaço do Conector](#search-connector-space) |Usado para encontrar objetos e Seguir um objeto e seus dados por meio do sistema. |

### <a name="delete"></a>Excluir
A ação de exclusão é usada com duas finalidades diferentes.  
![Captura de tela que mostra a janela "excluir conector" com "excluir somente espaço do conector" selecionado.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

A opção **Excluir apenas o espaço do conector** remove todos os dados, mas mantém a configuração.

A opção **Excluir Conector e Espaço** remove os dados e a configuração. Essa opção é usada quando você não deseja mais se conectar a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos do metaverso. Essa ação é uma operação demorada.

### <a name="configure-run-profiles"></a>Configurar perfis de execução
Esta opção permite que você veja os perfis de execução configurados para um Conector.

![Captura de tela que mostra a janela "Configurar perfis de execução" com a "importação Delta" selecionada.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Pesquisar Espaço do Conector
A ação de espaço do conector de pesquisa é útil para encontrar objetos e solucionar problemas de dados.

![Captura de tela que mostra a janela "Pesquisar espaço do conector".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Comece selecionando um **escopo**. É possível pesquisar com base nos dados (RDN, DN, Âncora, Subárvore) ou no estado do objeto (todas as outras opções).  
![Captura de tela que mostra o menu suspenso "escopo".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
 Por exemplo, se fizer uma pesquisa em Subárvore, você obterá todos os objetos em uma UO.  
![Captura de tela que mostra um exemplo de uma pesquisa "subárvore".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Nessa grade, é possível selecionar um objeto, selecionar **propriedades** e [segui-lo](tshoot-connect-object-not-syncing.md) do espaço conector de origem, passando pelo metaverso, até o espaço conector de destino.

### <a name="changing-the-ad-ds-account-password"></a>Alterando a senha da conta do AD DS
Se você alterar a senha da conta, o Serviço de Sincronização não poderá mais importar/exportar alterações para o AD local.   Você poderá ver o seguinte:

- A etapa de importação/exportação do conector do AD falha com o erro “no-start-credentials”.
- No Visualizador de Eventos do Windows, o log de eventos do aplicativo contém um erro com a ID do Evento 6000 e a mensagem “O agente de gerenciamento "contoso.com" não foi executado porque as credenciais eram inválidas”.

Para resolver o problema, atualize a conta de usuário do AD DS usando o seguinte:


1. Inicie o Synchronization Service Manager (INICIAR → Serviço de Sincronização).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Acesse a guia **Conectores**.
3. Selecione o Conector do AD que está configurado para usar a conta do AD DS.
4. Em Ações, selecione **Propriedades**.
5. Na caixa de diálogo pop-up, selecione Conectar-se à Floresta do Active Directory:
6. O nome da floresta indica o AD local correspondente.
7. O Nome de usuário indica a conta do AD DS usada para sincronização.
8. Insira a nova senha da conta do AD DS na caixa de texto ![Utilitário de Chave de Criptografia de Sincronização do Azure AD Connect](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png) da Senha
9. Clique em OK para salvar a nova senha e reinicie o Serviço de Sincronização para remover a senha antiga do cache de memória.



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
