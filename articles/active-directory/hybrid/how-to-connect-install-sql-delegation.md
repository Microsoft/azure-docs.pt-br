---
title: Instalar o Azure AD Connect usando permissões de administrador do SQL delegado | Microsoft Docs
description: Este tópico descreve uma atualização do Azure AD Connect que permite a instalação usando uma conta que tenha somente permissões de dbo do SQL.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85358660"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalar o Azure AD Connect usando permissões de administrador do SQL delegado
Antes da compilação mais recente do Azure AD Connect, não havia suporte para delegação administrativa ao implantar configurações que exigiam SQL.  Os usuários que desejavam instalar o Azure AD Connect precisavam ter permissões de administrador do servidor (SA) no servidor SQL.

Com o lançamento do Azure AD Connect, o provisionamento do banco de dados agora pode ser executado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário de banco de dados.

## <a name="before-you-begin"></a>Antes de começar
Para usar esse recurso, você precisa perceber que há várias partes móveis e cada uma delas pode envolver um administrador diferente em sua organização.  A tabela a seguir resume as funções individuais e suas respectivas tarefas na implantação do Azure AD Connect com esse recurso.

|Função|Descrição|
|-----|-----|
|Administrador do AD do Domínio ou Floresta|Cria a conta de serviço de nível de domínio que é usada pelo Azure AD Connect para executar o serviço de sincronização.  Para saber mais, confira [Contas e permissões](reference-connect-accounts-permissions.md).
|Administrador SQL|Cria o banco de dados ADSync e concede logon + acesso dbo ao administrador do Azure AD Connect e a conta de serviço criada pelo administrador do domínio/floresta.|
Administrador do Azure AD Connect|Instala o Azure AD Connect e especifica a conta de serviço durante a instalação personalizada.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Etapas para instalar o Azure AD Connect usando permissões do SQL delegado
Para provisionar o banco de dados fora da banda e instalar o Azure AD Connect com permissões do proprietário do banco de dados, use as etapas a seguir.

>[!NOTE]
>Embora não seja necessário, é **altamente recomendável** que a ordenação Latin1_General_CI_AS seja selecionada ao criar o banco de dados.


1. Peça ao Administrador do SQL para criar o banco de dados ADSync com uma sequência de ordenação sem diferenciar maiúsculas de minúsculas **(Latin1_General_CI_AS)**.  O banco de dados deve ser nomeado **ADSync**.  O modelo de recuperação, o nível de compatibilidade e o tipo de conteúdo serão atualizados para os valores corretos quando o Azure AD Connect for instalado.  No entanto, a sequência de ordenação deve ser definida corretamente pelo administrador do SQL ou então o Azure AD Connect bloqueará a instalação.  Para recuperar, o SA deve excluir e recriar o banco de dados.
 
   ![Collation](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Conceda as seguintes permissões ao administrador do Azure AD Connect e à conta de serviço do domínio:
   - Logon do SQL 
   - Direitos do **proprietário do banco de dados (dbo)**.
 
   ![Permissões](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect não dá suporte a logons com uma associação aninhada.  Isso significa que sua conta de administrador de Azure AD Connect e conta de serviço de domínio devem ser vinculadas a um logon que recebe direitos de dbo.  Ele não pode simplesmente ser o membro de um grupo atribuído a um logon com direitos de dbo.

3. Envie um email para o administrador do Azure AD Connect indicando o nome de servidor e instância do SQL que deve ser usado durante a instalação do Azure AD Connect.

## <a name="additional-information"></a>Informações adicionais
Uma vez provisionado o banco de dados, o administrador do Azure AD Connect pode instalar e configurar a sincronização local quando quiser.

Caso o administrador do SQL tenha restaurado o banco de dados ADSync de um backup de Azure AD Connect anterior, será necessário instalar o novo servidor de Azure AD Connect usando um banco de dados existente. Para obter mais informações sobre como instalar Azure AD Connect com um banco de dados existente, consulte [instalar Azure ad Connect usando um banco de dados AdSync existente](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Próximas etapas
- [Introdução ao Azure AD Connect usando configurações expressas](how-to-connect-install-express.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
- [Instalar o Azure AD Connect usando um banco de dados ADSync existente](how-to-connect-install-existing-database.md)  
