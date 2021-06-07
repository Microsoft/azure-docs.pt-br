---
title: Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a sincronização de atributos usando a tarefa de solução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317480"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect

## <a name="recommended-steps"></a>**Etapas Recomendadas**

Antes de investigar problemas de sincronização de atributo, vamos entender o processo de sincronização do **Azure AD Connect**:

  ![Processo de Sincronização do Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço do conector, uma tabela no banco de dados.
* **MV:** Metaverso, uma tabela no banco de dados.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Etapas de sincronização**

* Importar do AD: os objetos do Active Directory são trazidos para o AD CS.

* Importar do AAD: os objetos Azure Active Directory são trazidos para o AAD CS.

* Sincronização: **regras de sincronização de entrada** e **regras de sincronização de saída** são executadas na ordem de precedência número de menor para maior. Para exibir as Regras de Sincronização, você pode ir para **Editor de Regras de Sincronização** dos aplicativos da área de trabalho. As **Regras de Sincronização de Entrada** trazem dados de CS para MV. As **Regras de Sincronização de Saída** movem dados de MV para CS.

* Exportar para o AD: após a execução da sincronização, os objetos são exportados do AD CS para o **Active Directory**.

* Exportar para o AAD: após a execução da sincronização, os objetos são exportados do AAD CS para o **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Investigação passo a passo**

* Vamos começar nossa pesquisa do **Metaverso** e examinar o mapeamento de atributo de origem para destino.

* Inicie **Synchronization Service Manager** em aplicativos da área de trabalho, como mostrado abaixo:

  ![Inicializar o Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* No **Synchronization Service Manager**, selecione **Pesquisa de Metaverso**, selecione **Escopo por Tipo de Objeto**, selecione o objeto usando um atributo e clique no botão **Pesquisa**.

  ![Pesquisa de Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Clique duas vezes no objeto encontrado na pesquisar **Metaverso** para exibir todos os seus atributos. Você pode clicar na guia **Conectores** para examinar o objeto correspondente em todos os **Espaços do Conector**.

  ![Conectores de Objeto do Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Clique duas vezes no **Active Directory Connector** para exibir os atributos do **Espaço do Conector**. Clique no botão **Visualização**, na caixa de diálogo seguinte, clique no botão a **Gerar Visualização**.

  ![Captura de tela que mostra as propriedades do objeto espaço do conector com o botão Visualizar realçado.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Agora, clique no **Fluxo de Atributo de Importação**, isso mostra o fluxo de atributos do **Espaço do Active Directory Connector** para o **Metaverso**. A coluna **Regra de Sincronização** mostra quais **regras de sincronização** contribuem para esse atributo. A coluna **Fonte de Dados** mostra os atributos do **Espaço do Conector**. A coluna **Atributo de Metaverso** mostra os atributos no **Metaverso**. Você pode procurar o atributo que não está sincronizando aqui. Se você não encontrar o atributo aqui, ele não será mapeado e você precisará criar uma nova **Regra de Sincronização** personalizada para mapear o atributo.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique em **Exportar Fluxo de Atributos** no painel esquerdo para exibir o fluxo de atributos do **Metaverso** de volta no **Espaço do Active Directory Connector** usando **Regras de Sincronização de Saída**.

  ![Captura de tela que mostra o fluxo de atributo do metaverso de volta para Active Directory espaço do conector usando regras de sincronização de saída.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, você pode exibir o objeto **Espaço de Conector do Azure Active Directory** e gerar a **Visualização** para exibir o fluxo de atributos de **Metaverso** para o **Espaço de Conector** e vice-versa, dessa forma, você poderá investigar por que um atributo não está sincronizando.

## <a name="recommended-documents"></a>**Documentos recomendados**
* [Sincronização de Azure AD Connect: conceitos técnicos](./how-to-connect-sync-technical-concepts.md)
* [Sincronização do Azure AD Connect: noções básicas sobre a arquitetura](./concept-azure-ad-connect-sync-architecture.md)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure Active Directory Connect Sync: noções básicas sobre usuários, grupos e contatos](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Sincronização de Azure AD Connect: atributos de sombra](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Próximas etapas

- [Sincronização de Azure ad Connect](how-to-connect-sync-whatis.md).
- [O que é identidade híbrida?](whatis-hybrid-identity.md).