---
title: Implantação contínua do Azure Functions | Microsoft Docs
description: Use recursos de implantação contínua do Serviço de Aplicativo do Azure para publicar seu Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: fd8fa690c508b8bf748490668c1e9aaa811ac247
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731159"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions
As Funções do Azure facilitam a implantação do seu aplicativo de função utilizando a integração contínua do Serviço de Aplicativo. O Functions integra-se com BitBucket, Dropbox, GitHub e Azure DevOps. Isso permite que um fluxo de trabalho em que atualizações de código de função feitas utilizando um desses serviços integrados acione a implantação ao Azure. Se você for iniciante no Azure Functions, comece pela [Visão geral do Azure Functions](functions-overview.md).

A implantação contínua é uma ótima opção para projetos nos quais várias contribuições frequentes são integradas. Ele também permite manter o controle do código-fonte no código de funções. As seguintes fontes de implantação têm suporte atualmente:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositório externo (Git ou Mercurial)
* [Repositório local Git](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

As implantações são configuradas para cada aplicativo de função. Após a implantação contínua ser habilitada, o acesso ao código de função no portal é definido como *somente leitura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implantação contínua

Você deve ter a fonte de implantação configurada e o código de funções na fonte de implantação antes de configurar a implantação contínua. Em uma implantação de aplicativo de funções específica, cada função reside em um subdiretório nomeado, em que o nome do diretório é o nome da função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Para ser capaz de implantar do Azure DevOps, primeiro você deve vincular sua organização do Azure DevOps com sua assinatura do Azure. Para obter mais informações, confira [Configurar cobrança para sua organização do Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua
Utilize o procedimento a seguir para configurar a implantação contínua para um aplicativo de função existente. Essas etapas demonstram a integração com um repositório GitHub, porém etapas semelhantes se aplicam para serviços do Azure DevOps ou outros serviços de implantação.

1. Em seu aplicativo de função no [portal do Azure](https://portal.azure.com), clique em **Recursos da plataforma** e **Opções de implantação**. 
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, na folha **Implantações**, clique em **Configurar**.
 
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
3. Na folha **Origem da implantação**, clique em **Escolher fonte**, preencha as informações da origem de implantação escolhida e clique em **OK**.
   
    ![Escolher a fonte de implantação](./media/functions-continuous-deployment/choose-deployment-source.png)

Após a implantação contínua ser configurada, todas as alterações de arquivos na fonte de implantação são copiadas para o aplicativo de função e uma implantação completa do site é disparada. O site é reimplantado quando os arquivos da origem são atualizados.

## <a name="deployment-options"></a>Opções de implantação

A seguir estão alguns cenários comuns de implantação:

- [Criar uma implantação de preparo](#staging)
- [Mover as funções existentes para implantação contínua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

Aplicativos de Função ainda não dão suporte a slots de implantação. No entanto, você ainda pode gerenciar implantações de produção e preparo separadas usando a integração contínua.

O processo para configurar e trabalhar com uma implantação de preparo geralmente tem esta aparência:

1. Crie dois aplicativos de função em sua assinatura, um para o código de produção e outro para preparo. 

2. Crie uma fonte de implantação, se você ainda não tiver uma. Este exemplo usa [GitHub].

3. Para o aplicativo de funções de produção, conclua as etapas acima em **Configurar a implantação contínua** e defina a ramificação de implantação para a ramificação mestra do repositório GitHub.
   
    ![Escolher a ramificação de implantação](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita essa etapa para o aplicativo de funções de preparo, mas escolha a ramificação de preparo em vez do repositório GitHub. Se sua fonte de implantação não der suporte à ramificação, use uma pasta diferente.
    
5. Faça atualizações no código na ramificação de preparo ou na pasta e verifique se as alterações são refletidas na implantação de preparo.

6. Depois de testar, mescle alterações da ramificação de preparo na ramificação mestre. Essa mesclagem disparará a implantação para o aplicativo de função de produção. Se sua fonte de implantação não der suporte a ramificações, substitua os arquivos na pasta de produção pelos arquivos da pasta de preparo.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua
Quando houver funções existentes que você criou e manteve no portal, será preciso baixar os arquivos de código de função existentes usando o FTP ou o repositório Git local para poder configurar a implantação contínua conforme descrito acima. Você pode fazer isso nas configurações do Serviço de Aplicativo para seu aplicativo de função. Depois que os arquivos forem baixados, você poderá carregá-los na fonte de implantação contínua escolhida.

> [!NOTE]
> Depois de configurar a integração contínua, você não poderá mais editar os arquivos de origem no portal de Funções.

- [Como: configurar credenciais de implantação](#credentials)
- [Como: fazer o download de arquivos usando FTP](#downftp)
- [Como: fazer o download de arquivos usando o repositório Git local](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Como: configurar credenciais de implantação
Antes de baixar arquivos do aplicativo de funções com o FTP ou repositório Git local, você deve configurar suas credenciais para acessar o site. As credenciais são definidas no nível do aplicativo de Função. Utilize as etapas a seguir para definir as credenciais de implantação no portal do Azure:

1. Em seu aplicativo de função no [portal do Azure](https://portal.azure.com), clique em **Recursos da plataforma** e **Credenciais de implantação**.
   
    ![Definir credenciais de implantação local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Digite um nome de usuário e senha e clique em **Salvar**. Agora você pode usar essas credenciais para acessar seu aplicativo de função do FTP ou do repositório Git interno.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Como: fazer o download de arquivos usando FTP

1. No aplicativo de função do [Portal do Azure](https://portal.azure.com), clique em **recursos de Plataforma**e **Propriedades**, e copie os valores para **Usuário de FTP/Implantação**,**Nome do Host FTP** e**Nome de Host FTPS**.  

    O **Usuário de FTP/Implantação** deve ser inserido conforme exibido no portal, incluindo o nome do aplicativo, a fim de fornecer o contexto adequado para o servidor FTP.
   
    ![Obter as informações de implantação](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. No cliente de FTP, use as informações de conexão coletadas para se conectar ao aplicativo e baixar os arquivos de origem para suas funções.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Como: fazer o download de arquivos usando o repositório Git local

1. Em seu aplicativo de função no [portal do Azure](https://portal.azure.com), clique em **Recursos da plataforma** e **Opções de implantação**. 
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, na folha **Implantações**, clique em **Configurar**.
 
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Na folha **Fonte de implantações**, clique em**Repositório Git Local** e em seguida clique em **OK**.

3. Em **Recursos da plataforma**, clique em **Propriedades** e observe o valor da URL de Git. 
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Faça uma cópia do repositório em seu computador local usando uma linha de comando com reconhecimento do Git ou sua ferramenta de Git favorita. O comando Git clone é semelhante ao seguinte:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Busque arquivos de seu aplicativo de função para o clone no computador local, como no seguinte exemplo:
   
        git pull origin master
   
    Se solicitado, forneça as [credenciais de implantação configuradas](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
