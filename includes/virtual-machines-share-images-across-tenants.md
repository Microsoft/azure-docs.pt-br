---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "73903690"
---
Mas, se você quiser compartilhar imagens fora do seu locatário do Azure, em escala, você deve criar um registro de aplicativo para facilitar o compartilhamento.  O uso de um registro de aplicativo pode permitir cenários de compartilhamento mais complexos, como: 

* Gerenciamento de imagens compartilhadas quando uma empresa adquire outra, e a infraestrutura do Azure é distribuída entre locatários separados. 
* Os parceiros do Azure gerenciam a infraestrutura do Azure em nome de seus clientes. A personalização de imagens é feita dentro do locatário de parceiros, mas as implantações de infraestrutura ocorrerão no locatário do cliente. 


## <a name="create-the-app-registration"></a>Criar o registro do aplicativo

Crie um registro de aplicativo que será usado por ambos os locatários para compartilhar os recursos da Galeria de imagens.
1. Abra o [registros de aplicativo (visualização) no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecione **novo registro** no menu na parte superior da página.
1. Em **nome**, digite *myGalleryApp*.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Em **URI de redirecionamento**, digite *https://www.microsoft.com* e, em seguida, selecione **registrar**. Depois que o registro do aplicativo tiver sido criado, a página Visão geral será aberta.
1. Na página Visão geral, copie a **ID do aplicativo (cliente)** e salve-a para uso posterior.   
1. Selecione **certificados & segredos** e, em seguida, selecione **novo segredo do cliente**.
1. Em **Descrição**, digite *segredo do aplicativo entre locatário da Galeria de imagens compartilhadas*.
1. Em **expira**, deixe o padrão de **em 1 ano** e, em seguida, selecione **Adicionar**.
1. Copie o valor do segredo e salve-o em um local seguro. Você não pode recuperá-lo depois de sair da página.


Conceda à permissão de registro do aplicativo para usar a Galeria de imagens compartilhadas.
1. Na portal do Azure, selecione a Galeria de imagens compartilhada que você deseja compartilhar com outro locatário.
1. Selecione **selecionar controle de acesso (iam)** e, em **Adicionar atribuição de função** , selecione *Adicionar*. 
1. Em **função**, selecione **leitor**.
1. Em **atribuir acesso a:**, deixe isso como **usuário, grupo ou entidade de serviço do Azure ad**.
1. Em **selecionar**, digite *myGalleryApp* e selecione-o quando ele aparecer na lista. Quando terminar, selecione **Salvar**.


## <a name="give-tenant-2-access"></a>Dar acesso ao locatário 2

Conceda acesso de locatário 2 ao aplicativo solicitando uma entrada usando um navegador. Substitua *\<Tenant2 ID>* pela ID de locatário do locatário com o qual você gostaria de compartilhar sua galeria de imagens. Substitua *\<Application (client) ID>* pela ID do aplicativo do registro do aplicativo que você criou. Quando terminar de fazer as substituições, Cole a URL em um navegador e siga os prompts de entrada para entrar no locatário 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

No [portal do Azure](https://portal.azure.com) entre como locatário 2 e conceda ao registro do aplicativo acesso ao grupo de recursos no qual você deseja criar a VM.

1. Selecione o grupo de recursos e, em seguida, selecione **iam (controle de acesso)**. Em **Adicionar atribuição de função** , selecione **Adicionar**. 
1. Em **função**, digite **colaborador**.
1. Em **atribuir acesso a:**, deixe isso como **usuário, grupo ou entidade de serviço do Azure ad**.
1. Em **selecionar** tipo *myGalleryApp* , em seguida, selecione-o quando aparecer na lista. Quando terminar, selecione **Salvar**.

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.

