---
title: Guia de solução de problemas do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral das técnicas de depuração para o Gerenciador de armazenamento do Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: fd34ab7cd899549962663e8cee8ee2121c39c49e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840393"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas de Gerenciador de armazenamento do Azure

O Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, macOS e Linux. O aplicativo pode conectar contas de armazenamento hospedadas no Azure, Nuvens Nacionais e no Microsoft Azure Stack.

Este guia resume as soluções de problemas comuns encontrados no Gerenciador de Armazenamento.

## <a name="role-based-access-control-permission-issues"></a>Problemas de permissão de controle de acesso baseado em função

[Controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) fornece gerenciamento de acesso refinado de recursos do Azure, combinando conjuntos de permissões para _funções_. Aqui estão algumas sugestões que você pode seguir para obter o RBAC para trabalhar no Gerenciador de armazenamento.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>O que é necessário ver meus recursos no Gerenciador de armazenamento?

Se você estiver tendo problemas para acessar os recursos de armazenamento usando o RBAC, é possível que você ainda não foram atribuídos às funções apropriadas. As seções a seguir descrevem as permissões que atualmente requer que o Gerenciador de armazenamento para acessar os recursos de armazenamento.

Se você não tiver certeza de que ter as permissões ou funções apropriadas, entre em contato com seu administrador de conta do Azure.

#### <a name="read-listget-storage-accounts"></a>Ler: Listar/obter contas de armazenamento

Você deve ter permissão para listar contas de armazenamento. Você pode obter essa permissão que está sendo atribuído a função "Leitor".

#### <a name="list-storage-account-keys"></a>Listar chaves de conta de armazenamento

O Gerenciador de armazenamento também pode usar as chaves de conta para autenticar solicitações. Você pode obter acesso às chaves com as funções mais avançadas, como a função "Colaborador".

> [!NOTE]
> Chaves de acesso concedem permissões irrestritas a qualquer pessoa que mantém-los. Portanto, geralmente não se recomenda eles ser enviada para os usuários da conta. Se você precisar revogar as chaves de acesso, você pode regenerá-las a partir de [Portal do Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Funções de dados

Você deve ser atribuído a pelo menos uma função que concede acesso ler dados de recursos. Por exemplo, se você precisar listar ou baixar blobs, será necessário pelo menos a função "Leitor de dados de Blob de armazenamento".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que eu preciso de uma função de camada de gerenciamento para ver meus recursos no Gerenciador de armazenamento?

Armazenamento do Azure tem duas camadas de acesso: _management_ e _dados_. Assinaturas e contas de armazenamento são acessadas por meio da camada de gerenciamento. Contêineres, blobs e outros recursos de dados são acessados por meio da camada de dados. Por exemplo, se você quiser obter uma lista de suas contas de armazenamento do Azure, você envia uma solicitação para o ponto de extremidade de gerenciamento. Se você quiser uma lista de contêineres de blob em uma conta, você envia uma solicitação para o ponto de extremidade de serviço apropriado.

Funções de RBAC podem conter as permissões de acesso de camada de dados ou de gerenciamento. A função "Leitor", por exemplo, concede a você recursos de camada de gerenciamento de acesso somente leitura.

Estritamente falando, a função "Leitor" não fornece nenhuma permissão de camada de dados e não é necessária para acessar a camada de dados.

O Gerenciador de armazenamento torna fácil acessar os recursos reunindo as informações necessárias para se conectar aos recursos do Azure para você. Por exemplo, para exibir seus contêineres de blob, o Gerenciador de armazenamento envia uma solicitação da lista de contêineres para o ponto de extremidade de serviço de blob. Para obter esse ponto de extremidade, o Gerenciador de armazenamento pesquisará a lista de assinaturas e contas de armazenamento que você tem acesso a. Mas, para localizar suas assinaturas e contas de armazenamento, o Gerenciador de armazenamento também precisa de acesso para a camada de gerenciamento.

Se você não tiver uma função que conceda qualquer gerenciamento de permissões de camada, o Gerenciador de armazenamento não é possível obter as informações necessárias para conectar-se para a camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não é possível obter o gerenciamento de permissões de camada precisa do meu administrador?

Nós ainda não temos uma solução de RBAC no momento. Como alternativa, você pode solicitar um URI de SAS para [anexe ao seu recurso](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado autoassinado na cadeia de certificados (e erros semelhantes)

Erros de certificado são causados por uma das duas seguintes situações:

1. O aplicativo está conectado por meio de um "proxy transparente", o que significa que um servidor (como o servidor de sua empresa) está interceptando o tráfego HTTPS, descriptografando-o e então o criptografando usando um certificado autoassinado.
2. Você está executando um aplicativo que está injetando um certificado SSL autoassinado para as mensagens HTTPS recebidas. Exemplos de aplicativos que injetam certificados incluem software de inspeção de tráfego de antivírus e rede.

Quando o Gerenciador de Armazenamento vê um assinatura de autoatendimento ou o certificado não confiável, ele não sabe se a mensagem recebida do HTTPS foi alterada. Se você tiver uma cópia do certificado autoassinado, poderá permitir que o Gerenciador de Armazenamento confie nele realizando as seguintes etapas:

1. Obter a cópia codificada de Base 64 x. 509 (. cer) do certificado
2. Clique em **Editar** > **Certificados SSL** > **Importar Certificados** e, depois, use o seletor de arquivo para localizar, selecionar e abrir os arquivos .cer

Esse problema também pode ser o resultado de vários certificados (intermediário e raiz). Ambos os certificados devem ser adicionados para superar o erro.

Se você não tiver certeza de onde o certificado é proveniente, você pode tentar estas etapas para encontrá-lo:

1. Instalar o Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves deve ser suficiente)
    * Mac e Linux: deve estar incluído com o sistema operacional
2. Executar Open SSL
    * Windows: abra o diretório de instalação, clique em **/bin/** e clique duas vezes em **openssl.exe**.
    * Mac e Linux: execute **openssl** de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procurar certificados autoassinados. Se você não tiver certeza de quais certificados são autoassinados, procure onde o assunto `("s:")` e o emissor `("i:")` são os mesmos.
5. Depois de encontrar os certificados autoassinados, para cada um deles, copie e cole tudo a partir, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer.
6. Abra o Gerenciador de Armazenamento, clique em **Editar** > **Certificados SSL** > **Importar Certificados** e, depois, use o seletor de arquivo para localizar, selecionar e abrir os arquivos .cer que você criou.

Se você não encontrar todos os certificados autoassinados usando as etapas anteriores, entre em contato conosco por meio da ferramenta de comentários para obter mais ajuda. Você também pode optar por iniciar o Gerenciador de armazenamento na linha de comando com o `--ignore-certificate-errors` sinalizador. Quando iniciado com esse sinalizador, o Gerenciador de Armazenamento irá ignorar erros de certificado.

## <a name="sign-in-issues"></a>Problemas de credenciais

### <a name="blank-sign-in-dialog"></a>Diálogo de entrada em branco

Diálogos de entrada em branco são causados frequentemente pelo ADFS solicitando que o Gerenciador de armazenamento para executar um redirecionamento, que não é suportado pelo Electron. Para contornar esse problema, você pode tentar usar o fluxo de código de dispositivo para entrar. Para concluir as etapas abaixo, você precisa:

1. Menu: Visualização -> "Usem Sign-In de código de dispositivo".
2. Abra a caixa de diálogo Conectar (seja por meio do ícone de tomada na barra vertical à esquerda, ou “Adicionar conta” no painel da conta).
3. Escolha qual ambiente você deseja entrar.
4. Clique no botão "Entrar".
5. Siga as instruções no painel seguinte.

Se você estiver tendo problemas de inscrição para a conta que você deseja usar como navegador padrão já está conectado a uma conta diferente, você pode:

1. Copiar manualmente o link e o código para uma sessão privada do seu navegador.
2. Copiar manualmente o link e o código para um navegador diferente.

### <a name="reauthentication-loop-or-upn-change"></a>Loop de reautenticação ou alteração de UPN

Se você estiver em um loop de nova tentativa de autenticação, ou alterou o UPN de uma de suas contas, tente as seguintes etapas:

1. Remova todas as contas e, em seguida, feche o Gerenciador de Armazenamento
2. Exclua a pasta .IdentityService do seu computador. No Windows, a pasta está localizada em `C:\users\<username>\AppData\Local`. Para Mac e Linux, você pode encontrar a pasta na raiz do seu diretório de usuário.
3. Se você estiver no Mac ou Linux, também será necessário excluir a entrada de Microsoft.Developer.IdentityService no repositório de chaves do seu sistema operacional. No Mac, o repositório de chaves é o aplicativo de "Conjunto de Chaves Gnome". Para o Linux, o aplicativo geralmente é chamado de "Token de autenticação", mas o nome pode ser diferente dependendo da sua distribuição.

### <a name="conditional-access"></a>Acesso Condicional

Acesso condicional não tem suporte quando o Gerenciador de armazenamento está sendo usado no Windows 10, Linux ou macOS. Isso é devido a uma limitação na biblioteca do AAD usada pelo Gerenciador de armazenamento.

## <a name="mac-keychain-errors"></a>Erros de conjunto de chaves do Mac

O conjunto de chaves do macOS, às vezes, pode entrar em um estado que causa problemas para a biblioteca de autenticação do Gerenciador de Armazenamento. Para obter o conjunto de chaves desse estado, tente as seguintes etapas:

1. Feche o Gerenciador de Armazenamento.
2. Abra o conjunto de chaves (**cmd + espaço**, digite conjunto de chaves, clique enter).
3. Selecione o conjunto de chaves "logon".
4. Clique no ícone de cadeado para bloquear o conjunto de chaves (o cadeado será animado para uma posição bloqueada quando concluído, pode levar alguns segundos, dependendo de quais aplicativos que você tiver aberto).

    ![imagem](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Inicie o Gerenciador de Armazenamento.
6. Um pop-up deve aparecer dizendo algo como "O hub de serviços quer acessar o conjunto de chaves". Quando ele, insira sua senha de conta de administrador do Mac e clique em **sempre permitir** (ou **permitir** se **sempre permitir** não estiver disponível).
7. Tente entrar.

### <a name="general-sign-in-troubleshooting-steps"></a>Etapas gerais de solução de problemas de entrada

* Se você estiver no macOS, e a janela de logon nunca aparece sobre o "aguardando para autenticação..." caixa de diálogo, em seguida, tente [estas etapas](#mac-keychain-errors)
* Reiniciar o Gerenciador de Armazenamento
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Verifique se as suas configurações de proxy e de certificado estejam definidas para o seu computador e o Gerenciador de Armazenamento.
* Se você estiver no Windows e ter acesso ao Visual Studio de 2019 na mesma máquina e de entrada, tente entrar no Visual Studio de 2019. Após um entrar com êxito para o Visual Studio de 2019, você pode abrir o Gerenciador de armazenamento e ver a sua conta no painel de conta.

Se nenhum desses métodos funcionar [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Assinaturas ausentes e locatários desfeitos

Se você não conseguir recuperar suas assinaturas depois de entrar com êxito, tente os seguintes métodos:

* Verifique se sua conta tem acesso às assinaturas que você espera. Você pode verificar o seu acesso entrando no portal para o ambiente do Azure que você está tentando usar.
* Certifique-se de que você autenticou usando o Azure correto ambiente (do Azure, Azure China 21Vianet, Azure Alemanha, Azure US Government nos ou ambiente personalizado).
* Se você estiver atrás de um proxy, certifique-se de que você configurou o proxy de Gerenciador de armazenamento adequadamente.
* Tente remover e readicionar a conta.
* Se houver um link "Obter mais informações", procurar e ver quais mensagens de erro estão sendo geradas para os locatários que estão falhando. Se you'ren T' claro o que fazer com o erro mensagens você consulte e, em seguida, à vontade [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Não é possível remover o recurso de conta ou armazenamento anexado

Se você não conseguir remover uma conta anexada ou o recurso de armazenamento por meio da interface do usuário, você poderá excluir manualmente todos os recursos anexados, excluindo as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Feche o Gerenciador de Armazenamento antes de excluir as pastas acima.

> [!NOTE]
> Se você já importou todos os certificados SSL e o conteúdo de backup do diretório `certs`. Posteriormente, você pode usar o backup para reimportar os certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

Primeiro, certifique-se de que as seguintes informações inseridas estejam corretas:

* A URL do proxy e o número da porta
* Nome de usuário e senha, caso seja solicitado pelo proxy

> [!NOTE]
> O Gerenciador de armazenamento não dá suporte a arquivos de configuração automática de proxy para definir as configurações de proxy.

### <a name="common-solutions"></a>Soluções comuns

Se você ainda estiver enfrentando problemas, tente os seguintes métodos:

* Se você puder se conectar à Internet sem usar o proxy, verifique se o Gerenciador de Armazenamento funciona sem as configurações de proxy habilitadas. Se esse for o caso, talvez haja um problema com as configurações de proxy. Trabalhe com seu administrador de proxy para identificar os problemas.
* Verifique se outros aplicativos estão usando o servidor proxy conforme o esperado.
* Verifique se que você pode se conectar ao portal para o ambiente do Azure que você está tentando usar
* Verifique se que você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, deverá receber um InvalidQueryParameterValue ou resposta XML semelhante.
* Se outra pessoa também estiver usando o Gerenciador de Armazenamento com o servidor proxy, verifique se eles podem se conectar. Se eles puderem se conectar, talvez seja necessário entrar em contato com o administrador de seu servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se você tiver ferramentas de rede, como o Fiddler para Windows, você pode diagnosticar os problemas da seguinte maneira:

* Se você tiver que trabalhar por meio do proxy, será necessário configurar a ferramenta de rede para se conectar por meio do proxy.
* Verifique o número da porta usado por sua ferramenta de rede.
* Insira a URL do host local e o número de porta da ferramenta de rede como configurações de proxy no Gerenciador de Armazenamento. Quando feito corretamente, a ferramenta de rede passará a registrar solicitações de rede feitas pelo Gerenciador de Armazenamento para pontos de extremidade de serviço e de gerenciamento. Por exemplo, digite https://cawablobgrs.blob.core.windows.net/ para seu ponto de extremidade de blob em um navegador e você receberá uma resposta semelhante à seguinte, o que sugere que o recurso existe, embora você não pode acessá-lo.

![exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Entre em contato com o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, talvez seja necessário entrar em contato com seu administrador de servidor proxy e

* Certifique-se de que seu proxy não bloqueia o tráfego para pontos de extremidade do Azure de gerenciamento ou recurso.
* Verifique o protocolo de autenticação usado por seu servidor proxy. O Gerenciador de armazenamento atualmente não dá suporte a proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é Possível Recuperar Filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações de proxy estão corretas. Se você estiver concedido acesso a um recurso ao proprietário da assinatura ou conta, verifique se você leu ou lista de permissões para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Cadeia de caracteres de Conexão não ter parâmetros de configuração concluída

Se você receber essa mensagem de erro, é possível que você não tenha as permissões necessárias para obter as chaves para sua conta de armazenamento. Para confirmar se esse for o caso, acesse o portal e localize sua conta do Armazenamento. Você pode fazer isso rapidamente clicando duas vezes no nó para sua conta de armazenamento e clicando em "Abrir no Portal". Depois que você fizer isso, vá até a folha "Chaves de Acesso". Se você não tem permissões para exibir as chaves, você verá uma página com a mensagem "Você não tem acesso". Para contornar esse problema, você pode obter a chave de conta de outra pessoa e anexar com o nome e a chave, ou você pode pedir que alguém de uma SAS para a conta de armazenamento e usá-lo para anexar a conta de armazenamento.

Se você vir as chaves da conta, registre um problema no GitHub, portanto, podemos ajudá-lo a resolver o problema.

## <a name="issues-with-sas-url"></a>Problemas com a URL SAS

Se você estiver se conectando a um serviço usando uma URL SAS e enfrentando este erro:

* Verifique se a URL fornece as permissões necessárias para ler ou lista recursos.
* Verifique se a URL não expirou.
* Se a URL SAS tiver base em uma política de acesso, verifique se a política de acesso não foi revogada.

Se você anexou acidentalmente usando uma URL da SAS inválida e não é possível desanexá-la, execute as seguintes etapas:

1. Ao executar o Gerenciador de Armazenamento, pressione F12 para abrir a janela de ferramentas para desenvolvedores.
2. Clique na guia Aplicativo e clique em Armazenamento Local > file:// na árvore à esquerda.
3. Localize a chave associada ao tipo de serviço do URI SAS problemático. Por exemplo, se o URI da SAS incorreta for para um contêiner de blob, procure a chave nomeada `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. O valor da chave deve ser uma matriz JSON. Localize o objeto associado ao URI inválido e remova-o.
5. Pressione Ctrl+R para recarregar o Gerenciador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências do Linux

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> O Gerenciador de armazenamento como fornecido a. gz download só tem suporte para as distribuições do Ubuntu. Outras distribuições não tiverem sido verificadas e podem exigir pacotes adicionais ou alternativos.

Esses pacotes são os requisitos mais comuns para o Gerenciador de armazenamento no Linux:

* [O tempo de execução do .NET core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` ou `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> O Gerenciador de armazenamento versão 1.7.0 e anteriormente requerem o .NET Core 2.0. Se você tiver uma versão mais recente do .NET Core instalado, você precisará [Gerenciador de armazenamento de patch](#patching-storage-explorer-for-newer-versions-of-net-core). Se você estiver executando o Gerenciador de armazenamento 1.8.0 ou maior, em seguida, você poderá usar o para .NET Core 2.2. Versões posteriores 2.2 não tiverem sido verificadas para trabalhar no momento.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Baixe o Gerenciador de armazenamento.
2. Instalar o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Baixe o Gerenciador de armazenamento.
2. Instalar o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Baixar o Gerenciador de armazenamento
2. Instalar o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Baixar o Gerenciador de armazenamento
2. Instalar o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>O Gerenciador de armazenamento de aplicação de patches para as versões mais recentes do .NET Core

Para o Gerenciador de armazenamento 1.7.0 ou mais antigo, talvez você precise aplicar patch a versão do .NET Core usada pelo Gerenciador de armazenamento.

1. Baixe a versão 1.5.43 do StreamJsonRpc [do nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "Baixar o pacote" no lado direito da página.
2. Depois de baixar o pacote, altere sua extensão de arquivo do `.nupkg` para `.zip`.
3. Descompacte o pacote.
4. Abra a pasta `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Cópia `StreamJsonRpc.dll` nos seguintes locais dentro da pasta do Gerenciador de armazenamento:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Abrir no Explorer do portal do Azure não funciona

Se o botão "Abrir no Explorer" no portal do Azure não funciona para você, verifique se que você estiver usando um navegador compatível. Os navegadores a seguir foram testados para compatibilidade.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Próximas etapas

Se nenhuma das soluções funcionar para então [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Você pode também obter rapidamente GitHub, usando o botão "Relatar problema ao GitHub" no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)
