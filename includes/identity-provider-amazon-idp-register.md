---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82900349"
---
1. Entre no console do [desenvolvedor do Amazon](https://developer.amazon.com/dashboard) com suas credenciais de conta do Amazon.
1. Se você ainda não tiver feito isso, clique em **Inscrever-se**, siga as etapas de registro do desenvolvedor e aceite a política.
1. No painel, selecione **logon com Amazon**.
1. Selecione **Criar um Perfil de Segurança**.
1. Insira um **nome de perfil de segurança**, uma **Descrição do perfil de segurança**e uma URL de aviso de privacidade de **consentimento**, por exemplo, `https://www.contoso.com/privacy` a URL de aviso de privacidade é uma página que você gerencia, que fornece informações de privacidade aos usuários. Em seguida, clique em **Salvar**.
1. Na seção **logon com as configurações do Amazon** , selecione **o nome do perfil de segurança** que você criou, clique no ícone **gerenciar** e selecione **configurações da Web**.
1. Na seção **Configurações da Web**, copie os valores de **ID do cliente**. Selecione **Mostrar segredo** para obter o segredo do cliente e copie-o. Você precisará de ambos para configurar uma conta da Amazon como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Na seção **configurações da Web** , selecione **Editar**. Em **origens permitidas** e **URLs de retorno permitidas**, insira as URLs apropriadas (anotadas acima). 
1. Clique em **Salvar**.
