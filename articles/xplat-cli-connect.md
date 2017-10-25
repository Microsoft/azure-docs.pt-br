---
title: Fazer logon no Azure a partir da CLI | Microsoft Docs
description: "Conectar-se à assinatura do Azure a partir da CLI (Interface de Linha de Comando) do Azure para Mac, Linux e Windows"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Faça logon no Azure no Azure CLI
O Azure CLI é um conjunto de comandos de plataforma cruzada de software livre para trabalhar com recursos do Azure. Este artigo descreve as diferentes maneiras de fornecer as credenciais de conta do Azure para conectar o Azure CLI à sua assinatura do Azure:

* Execute o comando CLI `azure login` para autenticar por meio do Azure Active Directory. Este método fornece acesso a comandos CLI em ambos os [modos de comando](#cli-command-modes). Quando você executa o comando sem opções adicionais, o `azure login` solicitará que você continue efetuando logon interativamente por meio de um portal da Web. Para mais opções de comando do `azure login`, consulte os cenários neste artigo ou digite `azure login --help`.
* Se você precisar usar comandos CLI do modo do gerenciamento de serviços do Azure (não recomendado para a maioria das implantações novas), é possível baixar e instalar um arquivo de configurações de publicação no seu computador.

Se você ainda não tiver instalado a CLI, consulte [Instalar a CLI do Azure](cli-install-nodejs.md). Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](http://azure.microsoft.com/free/) em apenas alguns minutos.

Para obter informações sobre identidades de outra conta e sobre as assinaturas do Azure, confira [Como as assinaturas do Azure como são associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Cenário 1: logon do azure com logon interativo
Com certas contas, o CLI exige que você execute `azure login` e, em seguida, continue o processo de logon com um navegador da web por meio de um portal da web, um processo chamado *logon interativo*. Uma razão comum é quando você tem uma conta corporativa ou escolar (também chamada de uma *conta organizacional*) que é configurada para exigir autenticação multifator. Use também o logon interativo com sua conta da Microsoft quando desejar usar comandos do modo de Gerenciador de recursos.

O logon interativo é fácil: digite `azure login` – sem opções – conforme mostrado no exemplo a seguir:

```
azure login
```                                                                                             

A saída deve ter uma aparência semelhante ao seguinte:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Copie o código oferecido na saída do comando e abra em um navegador a página http://aka.ms/devicelogin ou outra página se especificado. (Você pode abrir um navegador no mesmo computador ou em outro computador ou dispositivo). Digite o código; em seguida, você será solicitado a inserir o nome de usuário e a senha para a identidade que deseja usar. Quando o processo for concluído, o shell de comando concluirá o logon. Ele poderia ser semelhante ao seguinte:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Com o logon interativo, a autenticação e a autorização são executadas com o Azure Active Directory. Se você usar uma identidade de conta da Microsoft, o processo de logon acessará seu domínio padrão do Azure Active Directory. (Se você se inscreveu para obter uma conta gratuita do Azure, o Azure Active Directory criou um domínio padrão para a sua conta).
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Cenário 2: logon do Azure com um nome de usuário e senha
Use o comando `azure login` com o parâmetro (`-u`) de nome de usuário para autenticação quando você deseja usar uma conta corporativa ou de estudante que não exija autenticação multifator. Você é solicitado na linha de comando para a senha (ou, opcionalmente, pode passar a senha como um parâmetro adicional do comando `azure login`). O exemplo a seguir passa o nome de usuário de uma conta organizacional:

    azure login -u myUserName@contoso.onmicrosoft.com

Você será solicitado a inserir sua senha:

    info:    Executing command login
    Password: *********

O processo de logon é concluído.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Se essa for a primeira vez fazendo logon com essas credenciais, você será solicitado a verificar se deseja armazenar um token de autenticação no cache. Esse aviso também ocorrerá se você tiver usado anteriormente o comando `azure logout` (descrito mais abaixo no artigo). Para ignorar esse aviso em cenários de automação, execute `azure login` com o parâmetro `-q`.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Cenário 3: logon do Azure com uma entidade de serviço
Se você criar uma entidade de serviço para um aplicativo do Active Directory e a entidade de serviço tiver permissões em sua assinatura, é possível usar o comando `azure login` para autenticar a entidade de serviço. Dependendo do cenário, você pode fornecer as credenciais da entidade de serviço como parâmetros explícitos do comando `azure login`. Por exemplo, o comando a seguir passa o nome da entidade do serviço e a ID de locatário do Active Directory:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Você será solicitado a inserir sua senha. Você também pode fornecer as credenciais por meio de um código de script ou aplicativo CLI ou usar um certificado para autenticar a entidade de serviço de maneira não interativa para cenários de automação. Para obter detalhes e exemplos, confira [Autenticando uma entidade de serviço com o Azure Resource Manager](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Cenário 4: usar um arquivo de configurações de publicação
Se você precisa apenas usar os comandos de CLI do modo Gerenciamento de Serviços do Azure (por exemplo, para implantar VMs do Azure no modelo de implantação clássico), pode se conectar usando um arquivo de configurações de publicação. Esse método instala um certificado no computador local que permite executar tarefas de gerenciamento durante o tempo em que a assinatura e o certificado forem válidos.

* **Para baixar o arquivo de configurações de publicação** para sua conta, certifique-se de que o CLI esteja no modo de Gerenciamento de Serviço digitando `azure config mode asm`. Em seguida, execute o seguinte comando:

        azure account download

Isso abre o navegador padrão e solicita que você entre no [portal clássico do Azure](https://manage.windowsazure.com). Depois de entrar, um arquivo `.publishsettings` é baixado. Anote onde esse arquivo está salvo.

> [!NOTE]
> Se a conta estiver associada a vários locatários do Active Directory do Azure, você deverá selecionar para qual Active Directory você deseja baixar um arquivo de configurações de publicação.
>
>

Depois de selecionado por meio da página de download ou acessando o Portal Clássico do Azure, o Active Directory escolhido se torna o padrão usado pelo portal clássico e pela página de download. Depois que um padrão tiver sido estabelecido, você verá o texto `**clique aqui para retornar à página de seleção**` na parte superior da página de download. Use o link fornecido para retornar à página de seleção.

* **Para importar o arquivo de configurações de publicação**, execute o seguinte comando:

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Depois de importar as suas configurações de publicação, exclua o arquivo `.publishsettings`. Ele não é mais exigido pelas CLI do Azure e apresenta um risco de segurança já que pode ser usado para obter acesso à sua assinatura.
>
>

## <a name="cli-command-modes"></a>Modos de comando da CLI
A CLI do Azure fornece dois modos de comando para trabalhar com recursos do Azure, com vários conjuntos de comandos:

* **Modo do Gerenciador de Recursos** – para trabalhar com recursos do Azure no modelo de implantação do Gerenciador de Recursos. Para definir esse modo, execute `azure config mode arm`.
* **Modo Gerenciamento de Serviços** – para trabalhar com recursos do Azure no modelo de implantação clássico. Para definir esse modo, execute `azure config mode asm`.

Quando instalado pela primeira vez, a versão atual do CLI está no modo do Gerenciador de recursos.

> [!NOTE]
> O modo do Gerenciador de Recursos e o modo do Gerenciamento de Serviços são mutuamente excludentes. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.
>
>

## <a name="multiple-subscriptions"></a>Várias assinaturas
Se você tiver várias assinaturas do Azure, a conexão ao Azure dará acesso a todas as assinaturas associadas às suas credenciais. Uma assinatura é selecionada como padrão e usada pela CLI do Azure durante a realização das operações. Você pode exibir as assinaturas, incluindo a assinatura padrão atual, usando o comando `azure account list`. Esse comando retorna informações semelhantes às seguintes:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Na lista anterior, a coluna **Atual** indica a assinatura padrão atual como Azure-sub-1. Para alterar a assinatura padrão, use o comando `azure account set` e especifique a assinatura que você deseja que seja a padrão. Por exemplo:

    azure account set Azure-sub-2

Isso muda a assinatura padrão para o Azure sub-2.

> [!NOTE]
> A alteração da assinatura padrão entra em vigor imediatamente e é uma alteração global. Novos comandos do Azure CLI, executados na mesma instância de linha de comando ou em outra instância, usarão a nova assinatura padrão.
>
>

Se quiser usar uma assinatura não padrão com a CLI do Azure, mas não quiser alterar o padrão atual, você poderá usar a opção `--subscription` do comando e fornecer o nome da assinatura que deseja usar na operação.

Uma vez conectado à sua assinatura do Azure, você pode começar a usar os comandos da CLI do Azure para trabalhar com recursos do Azure.

## <a name="storage-of-cli-settings"></a>Armazenamento de configurações da CLI
Quer você faça logon com o comando `azure login` ou importe as configurações de publicação, os seus logs e perfil CLI serão armazenados em um diretório `.azure` localizado no seu diretório `user`. O diretório `user` está protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user` . Você pode fazer isso das seguintes maneiras:

* No Windows, modifique as propriedades do diretório ou use o BitLocker.
* No Mac, ative o FileVault para o diretório.
* No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições do Linux oferecem recursos semelhantes.

## <a name="logging-out"></a>Fazendo logout
Para fazer logoff, use o seguinte comando:

    azure logout -u <username>

Se as assinaturas associadas à conta forem autenticadas apenas com o Active Directory, o logoff excluirá as informações da assinatura do perfil local. No entanto, se um arquivo de configurações de publicação também tiver sido importado para as assinaturas, o logoff excluirá apenas as informações relacionadas ao Active Directory do perfil local.

## <a name="next-steps"></a>Próximas etapas
* Para usar os comandos da CLI do Azure, confira [Comandos da CLI do Azure no modo do Gerenciador de Recursos](virtual-machines/azure-cli-arm-commands.md) e [comandos da CLI do Azure no modo Gerenciamento de Serviços do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).
* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
