---
title: "Azure Active Directory B2C: Referência: Como personalizar a IU de um percurso do usuário com políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 68f40aa638a687398512278a0b77d1ba392859cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalize a IU de um percurso do usuário com políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artigo é uma descrição avançada de como funciona a personalização da IU e como habilitar com políticas personalizadas B2C, usando o Identity Experience Framework


Uma experiência perfeita para o usuário é a chave para qualquer solução para negócios para consumidor. Quando mencionamos experiência perfeita para o usuário, queremos dizer uma experiência no dispositivo ou navegador, onde o percurso do usuário no nosso serviço não pode ser diferenciado do serviço do cliente que ele está usando.

## <a name="understand-the-cors-way-for-ui-customization"></a>Introdução ao método CORS para personalização da IU

O Azure AD B2C permite que você personalize a aparência da experiência do usuário (UX) em várias páginas que podem ser potencialmente atendidas e exibidas pelo Azure AD B2C por meio de suas políticas personalizadas.

Para essa finalidade, o Azure AD B2C executa o código no navegador do cliente e usa a abordagem moderna e padrão do [Compartilhamento de recursos entre origens (CORS)](http://www.w3.org/TR/cors/) para carregar conteúdo personalizado de uma URL particular, que você especifica em uma política personalizada para apontar para os modelos de HTML5/CSS. O CORS é um mecanismo que permite o uso recursos restritos, como fontes, em uma página da web solicitada em outro domínio fora do domínio do qual o recurso foi originado.

Em comparação com a maneira tradicional antiga, onde as páginas de modelo pertencentes à solução onde você forneceu textos e imagens limitados, onde o controle limitado de layout e aparência oferecidos prejudicam uma experiência perfeita, o método CORS dá suporte às HTML5 e CSS e permitem que você:

- Hospede o conteúdo e injete a solução dos seus controles usando o script no lado do cliente.
- Tenha controle total sobre cada pixel de layout e aparência.

Você pode fornecer quantas páginas de conteúdo quiser ao criar arquivos de HTML5/CSS conforme a conveniência.

> [!NOTE]
> Por motivos de segurança, bloqueamos a personalização do uso do JavaScript no momento. Para desbloquear o JavaScript, é necessário usar um nome de domínio personalizado para seu locatário Azure AD B2C.

Em cada um de seus modelos de HTML5/CSS, você deve fornecer um elemento *âncora*, que corresponde ao elemento `<div id=”api”>` necessário no HTML ou na página de conteúdo, como ilustram daqui em diante. O Azure AD B2C requer que todas as páginas de conteúdo tenham essa divisão específica.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

O conteúdo relacionado ao Azure AD B2C para a página será injetado nessa divisão, enquanto o restante da página fica sob o seu controle. O código JavaScript do Azure AD B2C extrai seu conteúdo e injeta o HTML dentro desse elemento de divisão específico. O Azure AD B2C injeta os seguintes controles conforme a conveniência: controle de seletor de conta, controles de logon, controles de vários fatores (atualmente baseados em telefone) e controles de coleta de atributo de conta. O Azure AD B2C garante que todos os controles HTML5 estejam acessíveis e em conformidade, que todos os controles possam ser totalmente estilizados e que a versão daquele controle não seja regredida.

O conteúdo mesclado, por fim, é exibido como o documento dinâmico para o consumidor.

Para garantir que as funções acima funcionem corretamente, faça o seguinte:

- Certifique-se de que seu conteúdo esteja em conformidade com HTML5 e acessível
- Verifique se que o servidor de conteúdo está habilitado para CORS.
- Forneça conteúdo por HTTPS.
- Use URLS absolutas como https://yourdomain/content para todos os links e conteúdo CSS.

> [!TIP]
> Para verificar se o site que você está hospedando o conteúdo possui CORS habilitado e testar solicitações CORS, use o site http://test-cors.org/. Graças a esse site, você pode simplesmente enviar a solicitação CORS para um servidor remoto (para testar se há suporte para CORS), ou enviar a solicitação CORS para um servidor de teste (para explorar alguns recursos do CORS).

> [!TIP]
> O site http://enable-cors.org/ também apresenta mais recursos úteis no CORS.

Graças a essa abordagem baseada em CORS, os usuários finais terão experiências consistentes entre seu aplicativo e as páginas atendidas pelo Azure AD B2C.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Como pré-requisito, você precisa criar uma conta de armazenamento. Você precisará de uma assinatura do Azure para criar uma conta de Armazenamento de Blobs do Azure. Você pode se inscrever para uma avaliação gratuita no [site do Azure](https://azure.microsoft.com/en-us/pricing/free-trial/).

1. Abra uma sessão de navegação e navegue até o [portal do Azure](https://portal.azure.com).
2. Entre com suas credenciais administrativas.
3. Clique em **Novo** > **Dados + armazenamento** > **Conta de armazenamento**.  Uma folha **Criar conta de armazenamento** abre.
4. No **nome**, forneça um nome para a conta de armazenamento, por exemplo, *contoso369b2c*. Esse valor será referenciado mais tarde como *storageAccountName*.
5. Escolha as seleções apropriadas para o tipo de preços, o grupo de recursos e a assinatura. Verifique se a opção **Fixar no Quadro Inicial** está marcada. Clique em **Criar**.
6. Volte para o quadro inicial e clique na conta de armazenamento que você acabou de criar.
7. Na seção **serviços**, clique em **Blobs**. A **folha do serviço Blob** abre.
8. Clique em **+ contêiner**.
9. No **nome**, forneça um nome para o contêiner, por exemplo, *b2c*. Esse valor será posteriormente referenciado como *containerName*.
9. Selecione **Blob** como o **Tipo de acesso**. Clique em **Criar**.
10. O contêiner que você criou aparecerá na lista da **folha de serviço Blob**.
11. Feche a folha **Blobs** .
12. Na **folha da conta de armazenamento**, clique o ícone **Chave**. Uma **folha chaves de acesso** abre.  
13. Anote o valor de **key1**. Esse valor será referenciado mais tarde como *key1*.

## <a name="downloading-the-helper-tool"></a>Como baixar a ferramenta auxiliar

1.  Baixe a ferramenta auxiliar do [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Salve o arquivo *B2C-AzureBlobStorage-Client-master.zip* no seu computador local.
3.  Extraia o conteúdo do arquivo B2C-AzureBlobStorage-Client-master.zip no seu disco local, por exemplo, na pasta **UI-Customization-Pack**. Isso criará uma pasta *B2C-AzureBlobStorage-Client-master*.
4.  Abra a pasta e extraia o conteúdo do arquivo *B2CAzureStorageClient.zip* nela.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carregue os arquivos de exemplo do UI-Customization-Pack

1.  Usando o Windows Explorer, navegue até a pasta *B2C-AzureBlobStorage-Client-master* localizado na pasta *UI-Customization-Pack* criada na seção anterior.
2.  Execute o arquivo *B2CAzureStorageClient.exe*. Esse programa simplesmente carregará todos os arquivos no diretório que você especifica para sua conta de armazenamento e habilita o acesso CORS para esses arquivos.
3.  Quando solicitado, especifique: um.  O nome da sua conta de armazenamento *storageAccountName*, por exemplo, *contoso369b2c*.
    b.  A chave de acesso primária de seu armazenamento de BLOBs do Azure, *key1*, por exemplo, *contoso369b2c*.
    c.  O nome do seu contêiner de armazenamento de blobs de armazenamento, *containerName*, por exemplo, *b2c*.
    d.  O caminho dos arquivos de exemplo *Pacote Starter*, por exemplo *..\B2CTemplates\wingtiptoys*.

Se você seguiu as etapas acima, os HTML5 e arquivos CSS do *UI-Customization-Pack* para a empresa fictícia **wingtiptoys** apontará para a sua conta de armazenamento.  Para verificar se o conteúdo foi carregado corretamente, abra a folha de contêineres relacionados no portal do Azure. Como alternativa, você pode verificar se o conteúdo foi carregado corretamente, para isso, acesse a página de um navegador. Para obter mais informações, confira [Azure Active Directory B2C: uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface de usuário (IU) da página](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Certifique-se de que a conta de armazenamento tenha o CORS habilitado

O CORS (compartilhamento de recursos entre origens) deve estar habilitado no seu ponto de extremidade para que o Azure AD B2C Premium carregue o seu conteúdo. Isso ocorre porque seu conteúdo é hospedado em um domínio diferente do domínio do qual o Azure AD B2C Premium fornece a página.

Para verificar se o armazenamento que está hospedando o conteúdo tem CORS habilitado, prossiga com as etapas a seguir:

1. Abra uma sessão de navegação e navegue até a página *unified.html* usando a URL completa do seu local na sua conta de armazenamento `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por exemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navegue até http://test-cors.org. Este site permite que você verifique se a página que você está usando tem CORS habilitado.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. Na **URL remota**, insira a URL completa para o seu conteúdo unified.html e clique em **Enviar solicitação**.
4. Verifique se a saída da seção **Resultados** contém *XHR status: 200*. Isso indica que o CORS está habilitado.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Agora, a conta de armazenamento deve conter um contêiner de blobs denominado *b2c* na nossa ilustração, que contém os seguintes modelos wingtiptoys do *Pacote Starter*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A tabela a seguir descreve a finalidade das páginas do HTML5 acima.

| Modelo do HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Esta página pode ser usada como modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Esta página pode ser usada como modelo para uma página de esquecimento de senha. |
| *selfasserted.html* | Esta página pode ser usada como modelo para uma página de inscrição de conta social ou uma página de inscrição de conta local. |
| *unified.html* | Esta página pode ser usada como modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Esta página pode ser usada como modelo para uma página de atualização de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adicione um link aos modelos de HTML5/CSS para o seu percurso do usuário

Você pode adicionar um link para os modelos de HTML5/CSS ao seu percurso do usuário ao editar diretamente uma política personalizada.

Os modelos de HTML5/CSS personalizados usados no seu percurso do usuário devem ser especificados em uma lista de definições de conteúdo que podem ser usados nos percursos do usuário. Para essa finalidade, um elemento XML opcional *<ContentDefinitions>* deve ser declarado na seção *<BuildingBlocks>* do arquivo XML de política personalizada.

A tabela a seguir descreve o conjunto de ids de definição de conteúdo reconhecido pelo mecanismo de experiência de identidade de Azure AD B2C e o tipo de páginas relacionadas a eles.

| Id de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *api.error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | **Página de seleção de provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Esses são os provedores de identidade empresarial ou provedores de identidade social, como Facebook e Google+, ou contas locais (baseadas em endereço de email ou nome de usuário). |
| *api.idpselections.signup* | **Seleção de provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Esses são os provedores de identidade empresarial ou provedores de identidade social, como Facebook e Google+, ou contas locais (baseadas em endereço de email ou nome de usuário). |
| *api.localaccountpasswordreset* | **Página de esquecimento de senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar sua redefinição de senha.  |
| *api.localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de inscrição que o usuário deve preencher ao entrar usando uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição que o usuário deve preencher ao inscrever-se usando uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | **Página de autenticação multifator**. Nesta página, os usuários pode verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que deve ser preenchida pelo usuário quando o mesmo se inscreve usando uma conta existente de um provedor de identidade social, como o Facebook ou Google+. Esta página é semelhante à página de inscrição de conta social acima, exceto os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que o usuário pode usar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta social acima, exceto os campos de entrada de senha. |
| *api.signuporsignin* | **Página de inscrição ou entrada unificada**.  Esta página controla tanto a inscrição quanto a entrada de usuários que podem usar provedores de identidade empresarial ou provedores de identidade social, como Facebook, Google+ ou contas locais.

## <a name="next-steps"></a>Próximas etapas
[Referência: Aprenda como as políticas personalizadas trabalham com o Identity Experience Framework no B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
