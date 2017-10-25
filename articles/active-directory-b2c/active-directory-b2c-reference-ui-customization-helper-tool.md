---
title: "Azure Active Directory B2C: ferramenta auxiliar de personalização da IU da página | Microsoft Docs"
description: "Uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface do usuário da página no Active Directory B2C do Azure"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
ms.openlocfilehash: e0c2d827553567ddbc7d006192dc35574e66f1cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface de usuário (IU) da página
Este artigo é um complemento do [artigo principal sobre a personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md) no Active Directory B2C do Azure (AD do Azure). As etapas a seguir descrevem como usufruir do recurso de personalização da interface do usuário da página usando conteúdo de exemplo HTML e CSS que fornecemos.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um locatário do Azure AD B2C
Para personalizar algo, você precisará [obter um locatário do AD B2C do Azure](active-directory-b2c-get-started.md) , caso ainda não tenha um.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou credenciais
O conteúdo de exemplo fornecido pode ser usado para personalizar duas páginas em uma [política de inscrição ou de entrada](active-directory-b2c-reference-policies.md): a [página de entrada unificada](active-directory-b2c-reference-ui-customization.md) e a [página de atributos autodeclarados](active-directory-b2c-reference-ui-customization.md). Ao [criar a política de inscrição ou de entrada](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adicione Conta Local (endereço de email), o Facebook, o Google+ e a Microsoft como **Provedores de identidade**. Esses são os únicos IDPs que nosso conteúdo HTML de exemplo aceitará.  Você também pode adicionar um subconjunto desses IDPs, se desejar.

## <a name="register-an-application"></a>Registrar um aplicativo
Você precisará [registrar um aplicativo](active-directory-b2c-app-registration.md) no locatário B2C que possa ser usado para executar a política. Depois de registrar o aplicativo, você terá algumas opções que poderão ser usadas para executar de fato a política de inscrição:

* Compilar um dos aplicativos de início rápido do AD B2C do Azure listados na seção "Introdução" de [Entrada e inscrição de consumidores em seus aplicativos](active-directory-b2c-overview.md#get-started).
* Use o aplicativo predefinido [Playground do AD B2C do Azure](https://aadb2cplayground.azurewebsites.net) . Caso opte por usar o playground, você deverá registrar um aplicativo no locatário B2C usando o **URI de redirecionamento** `https://aadb2cplayground.azurewebsites.net/`.
* Na política, use o botão **Executar Agora** no [Portal do Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalizar a política
Para personalizar a aparência da sua política, primeiro será necessário criar arquivos HTML e CSS usando as convenções específicas do AD B2C do Azure. Em seguida, carregue o conteúdo estático em um local publicamente disponível para que o Azure AD B2C possa acessá-lo. Esse local pode ser seu próprio servidor Web dedicado, o Armazenamento de Blobs do Azure, a Rede de Distribuição de Conteúdo do Azure ou qualquer outro recurso estático que esteja hospedando o provedor. Os únicos requisitos são que o conteúdo esteja disponível por HTTPS e possa ser acessado usando CORS. Depois de expor o conteúdo estático na Web, é possível editar a política para apontar para esse local e apresentar esse conteúdo aos clientes. O [artigo principal sobre personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md) descreve detalhadamente como funciona o recurso de personalização do Azure AD B2C.

Para os fins deste tutorial, já criamos o conteúdo de exemplo e o hospedamos no Armazenamento de Blobs do Azure. O conteúdo de exemplo é uma personalização bastante básica no tema de nossa empresa fictícia, "Wingtip Toys". Para testá-lo em sua própria política, siga estas etapas:

1. Entre em seu locatário no [portal do Azure](https://portal.azure.com/) e navegue até a folha de recursos B2C.
2. Clique em **Políticas de inscrição ou de entrada** e clique em sua política (por exemplo, "b2c1signupsignin\_1\_sign\_up\_sign\_in").
3. Clique na **Personalização da interface do usuário da Página** e na **Página de inscrição ou de entrada unificada**.
4. Alterne a opção **Usar página personalizada** para **Sim**. No campo **URI da página personalizada**, digite `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Clique em **OK**.
5. Clique em **Página de inscrição da conta local**. Alterne a opção **Usar modelo personalizado** para **Sim**. No campo **URI da página personalizada**, digite `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Repita a mesma etapa para a **Página de inscrição de conta social**.
   Clique em **OK** duas vezes para fechar as folhas de personalização da interface do usuário.
7. Clique em **Salvar**.

Agora, você pode testar a política personalizada. Você pode usar seu próprio aplicativo ou o playground do AD B2C do Azure se desejar, mas também pode simplesmente clicar no comando **Executar Agora** na folha da política. Selecione o aplicativo na caixa suspensa e escolha o URI de redirecionamento apropriado. Clique no botão **Executar agora** . Uma nova guia do navegador se abre e você pode executar a experiência de inscrever-se para o aplicativo com o novo conteúdo já pronto!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Carregar o conteúdo de exemplo no Armazenamento de Blobs do Azure
Se quiser usar o Armazenamento de Blobs do Azure para hospedar o conteúdo da página, você poderá criar sua própria conta de armazenamento e usar nossa ferramenta auxiliar do B2C para carregar os arquivos.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **Dados + armazenamento** > **Conta de armazenamento**. Você precisará de uma assinatura do Azure para criar uma conta de Armazenamento de Blobs do Azure. Você pode se inscrever para uma avaliação gratuita no [site do Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Forneça um **Nome** para o armazenamento de conta (por exemplo, "contoso") e selecione as seleções apropriadas para **Tipo de preço**, **Grupo de recursos** e **Assinatura**. Verifique se a opção **Fixar no Quadro Inicial** está marcada. Clique em **Criar**.
4. Volte para o quadro inicial e clique na conta de armazenamento que você acabou de criar.
5. Na seção **Resumo**, clique em **Contêineres** e em **+Adicionar**.
6. Forneça um **Nome** para o contêiner (por exemplo, "b2c") e selecione **Blob** como o **Tipo de acesso**. Clique em **OK**.
7. O contêiner que você criou aparecerá na lista da folha **Blobs** . Anote a URL do contêiner; por exemplo, ela deve ter a seguinte aparência: `https://contoso.blob.core.windows.net/b2c`. Feche a folha **Blobs** .
8. Na folha do armazenamento de conta, clique em **Chaves** e anote os valores dos campos **Nome da Conta de Armazenamento** e **Chave de Acesso Primária**.

> [!NOTE]
> **Chave de Acesso Primária** é uma credencial de segurança importante.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Baixar a ferramenta auxiliar e os arquivos de exemplo
Você pode baixar a [ferramenta auxiliar Armazenamento de Blobs do Azure e os arquivos de exemplo como um arquivo .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloná-los do GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Esse repositório contém um diretório `sample_templates\wingtip` , que inclui o exemplo HTML, CSS e as imagens. Para que esses modelos façam referência à sua própria conta de Armazenamento de Blobs do Azure, será necessário editar os arquivos HTML. Abra `unified.html` e `selfasserted.html` e substitua todas as instâncias de `https://localhost` pela URL do seu próprio contêiner que você anotou nas etapas anteriores. É necessário usar o caminho absoluto dos arquivos HTML pois, nesse caso, o HTML será atendido pelo Azure AD, sob o domínio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Carregar os arquivos de exemplo
No mesmo repositório, descompacte `B2CAzureStorageClient.zip` e execute o arquivo `B2CAzureStorageClient.exe`. Esse programa simplesmente carregará todos os arquivos no diretório que você especifica para sua conta de armazenamento e habilita o acesso CORS para esses arquivos. Se você seguiu as etapas acima, os arquivos HTML e CSS agora estarão apontando para a conta de armazenamento. Observe que o nome da sua conta de armazenamento é a parte que precede `blob.core.windows.net` por exemplo, `contoso`. Você pode verificar se o conteúdo foi carregado corretamente, tentando acessar `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` em um navegador. Use também [http://test-cors.org/](http://test-cors.org/) para certificar-se de que o conteúdo agora está habilitado para CORS. (Procure por "XHR status: 200" no resultado.)

### <a name="customize-your-policy-again"></a>Personalizar a política, novamente
Agora que você carregou o conteúdo de exemplo em sua própria conta de armazenamento, é preciso editar a política de inscrição para fazer referência a ela. Repita as etapas da seção acima ["Personalizar a política"](#customize-your-policy) , desta vez usando as URLs da sua própria conta de armazenamento. Por exemplo, o local do arquivo `unified.html` será `<url-of-your-container>/wingtip/unified.html`.

Agora, você pode usar o botão **Executar Agora** ou seu próprio aplicativo para executar a política novamente. O resultado deve ser quase o mesmo; você usou o mesmo HTML e CSS de exemplo em ambos os casos. No entanto, as políticas agora estão fazendo referência à sua própria instância do Armazenamento de Blobs do Azure, e você é livre para editar e recarregar os arquivos como quiser. Para saber mais sobre como personalizar o HTML e CSS, confira o [artigo principal sobre personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md).

