---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 9a5b3b04287a8b732d01bd8fe4610e073332da0d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478960"
---
1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **Propriedades**. Cópia da **ID de diretório** em um arquivo temporário. Você usará esse valor para configurar o aplicativo de exemplo na próxima seção.

    ![ID do diretório do Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o **registros do aplicativo (herdado)** painel. Selecione o **novo registro de aplicativo** botão.

1. Dê um nome amigável para esse registro de aplicativo na caixa **Nome**. Escolha **Tipo de aplicativo** como **Nativo** e **URI de Redirecionamento** como `https://microsoft.com`. Selecione **Criar**.

    ![Criar painel](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Abra o aplicativo registrado e copie o valor do campo **ID do aplicativo** em um arquivo temporário. Esse valor identifica seu aplicativo do Azure Active Directory. Você usará a ID do aplicativo para configurar o aplicativo de exemplo nas seções a seguir.

    ![ID do aplicativo do Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Abra o painel de registro de aplicativo. Selecione **Configurações** > **Permissões necessárias** e então:

   a. Selecione **Adicionar** na parte superior esquerda para abrir o painel **Adicionar acesso à API**.

   b. Escolha **Selecionar uma API** e pesquise **Gêmeos Digitais do Azure**. Se sua pesquisa não localizar a API, pesquise por **Espaços Inteligentes do Azure** em vez disso.

   c. Selecione a opção **Gêmeos Digitais do Azure (Serviço de Espaços Inteligentes do Azure)** e escolha **Selecionar**.

   d. Escolha **Selecionar permissões**. Marque a caixa de seleção **Acesso de Leitura/Gravação** de permissões delegadas e escolha **Selecionar**.

   e. Selecione **Concluído** no painel **Adicionar acesso à API**.

   f. No painel **Permissões necessárias**, selecione o botão **Conceder permissões** e, em seguida, aceite a confirmação que aparece. Se a permissão não é concedida para esta API, contate o administrador.

      ![Painel Permissões necessárias](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 