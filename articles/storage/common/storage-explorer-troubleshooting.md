---
title: Guia de solução de problemas do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral das técnicas de depuração para Gerenciador de Armazenamento do Azure
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 15df9b38abe35fe3eefad2fa160e1c1f16fe7aa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439452"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas do Gerenciador de Armazenamento do Azure

O Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que facilita o trabalho com dados do Armazenamento do Azure no Windows, macOS e Linux. O aplicativo pode conectar contas de armazenamento hospedadas no Azure, em nuvens nacionais e no Azure Stack.

Este guia resume soluções para problemas que normalmente são vistos em Gerenciador de Armazenamento.

## <a name="azure-rbac-permissions-issues"></a>Problemas de permissões do RBAC do Azure

Controle de acesso baseado em função do Azure o [RBAC do Azure](../../role-based-access-control/overview.md) permite o gerenciamento de acesso altamente granular dos recursos do Azure combinando conjuntos de permissões em _funções_. Aqui estão algumas estratégias para colocar o RBAC do Azure funcionando de maneira ideal no Gerenciador de Armazenamento.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Como fazer acessar meus recursos no Gerenciador de Armazenamento?

Se você estiver tendo problemas para acessar os recursos de armazenamento por meio do RBAC do Azure, talvez você não tenha atribuído as funções apropriadas. As seções a seguir descrevem as permissões Gerenciador de Armazenamento atualmente exige o acesso aos recursos de armazenamento. Entre em contato com o administrador da conta do Azure se você não tiver certeza de que tem as funções ou permissões apropriadas.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problema de permissões de "leitura: listar/obter conta de armazenamento"

Você deve ter permissão para listar contas de armazenamento. Para obter essa permissão, você deve receber a função _leitor_ .

#### <a name="list-storage-account-keys"></a>Listar chaves da conta de armazenamento

Gerenciador de Armazenamento também pode usar chaves de conta para autenticar solicitações. Você pode obter acesso a chaves de conta por meio de funções mais poderosas, como a função _colaborador_ .

> [!NOTE]
> Chaves de acesso concedem permissões irrestritas a qualquer pessoa que as mantenha. Portanto, não recomendamos que você entregue essas chaves para os usuários da conta. Se você precisar revogar chaves de acesso, poderá gerá-las novamente a partir do [portal do Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Funções de dados

Você deve receber pelo menos uma função que conceda acesso para ler dados de recursos. Por exemplo, se você quiser listar ou baixar BLOBs, precisará de pelo menos a função de _leitor de dados de blob de armazenamento_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que preciso de uma função de camada de gerenciamento para ver meus recursos no Gerenciador de Armazenamento?

O armazenamento do Azure tem duas camadas de acesso: _Gerenciamento_ e _dados_. As assinaturas e as contas de armazenamento são acessadas por meio da camada de gerenciamento. Contêineres, BLOBs e outros recursos de dados são acessados por meio da camada de dados. Por exemplo, se você quiser obter uma lista de suas contas de armazenamento do Azure, envie uma solicitação para o ponto de extremidade de gerenciamento. Se você quiser uma lista de contêineres de BLOB em uma conta, envie uma solicitação para o ponto de extremidade de serviço apropriado.

As funções do Azure podem conceder permissões para acesso à camada de dados ou gerenciamento. A função leitor, por exemplo, concede acesso somente leitura aos recursos da camada de gerenciamento.

Estritamente falando, a função leitor não fornece nenhuma permissão de camada de dados e não é necessária para acessar a camada de dados.

Gerenciador de Armazenamento facilita o acesso aos recursos coletando as informações necessárias para se conectar aos recursos do Azure. Por exemplo, para exibir seus contêineres de BLOB, Gerenciador de Armazenamento envia uma solicitação de "listar contêineres" para o ponto de extremidade do serviço BLOB. Para obter esse ponto de extremidade, Gerenciador de Armazenamento pesquisa a lista de assinaturas e contas de armazenamento às quais você tem acesso. Para localizar suas assinaturas e contas de armazenamento, Gerenciador de Armazenamento também precisa acessar a camada de gerenciamento.

Se você não tiver uma função que conceda permissões de camada de gerenciamento, Gerenciador de Armazenamento não poderá obter as informações necessárias para se conectar à camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não conseguir obter as permissões da camada de gerenciamento que preciso do meu administrador?

Se você quiser acessar contêineres de BLOB, ADLS Gen2 contêineres ou diretórios, ou filas, você pode anexar a esses recursos usando suas credenciais do Azure.

1. Abra a caixa de diálogo Conectar.
1. Selecione o tipo de recurso ao qual você deseja se conectar.
1. Selecione **entrar usando Azure Active Directory (Azure AD)**. Selecione **Avançar**.
1. Selecione a conta de usuário e o locatário associados ao recurso ao qual você está anexando. Selecione **Avançar**.
1. Insira a URL para o recurso e insira um nome de exibição exclusivo para a conexão. Selecione **Avançar** e **conectar**.

Para outros tipos de recursos, atualmente não temos uma solução relacionada ao RBAC do Azure. Como alternativa, você pode solicitar uma URL SAS e, em seguida, anexar ao recurso seguindo estas etapas:

1. Abra a caixa de diálogo Conectar.
1. Selecione o tipo de recurso ao qual você deseja se conectar.
1. Selecione **SAS (assinatura de acesso compartilhado)**. Selecione **Avançar**.
1. Insira a URL SAS que você recebeu e insira um nome de exibição exclusivo para a conexão. Selecione **Avançar** e **conectar**.
 
Para obter mais informações sobre como anexar recursos, consulte [anexar a um recurso individual](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Funções internas do Azure recomendadas

Há várias funções internas do Azure que podem fornecer as permissões necessárias para usar Gerenciador de Armazenamento. Algumas dessas funções são:
- [Proprietário](../../role-based-access-control/built-in-roles.md#owner): gerencie tudo, incluindo o acesso aos recursos.
- [Colaborador](../../role-based-access-control/built-in-roles.md#contributor): gerenciar tudo, excluindo o acesso aos recursos.
- [Leitor](../../role-based-access-control/built-in-roles.md#reader): ler e listar recursos.
- [Colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor): gerenciamento completo de contas de armazenamento.
- [Proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): acesso completo aos dados e contêineres de blob do armazenamento do Azure.
- [Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): ler, gravar e excluir contêineres e blobs de armazenamento do Azure.
- [Leitor de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Leia e liste contêineres e blobs de armazenamento do Azure.

> [!NOTE]
> As funções de colaborador de conta de armazenamento, proprietário e colaborador concedem acesso à chave da conta.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: certificado autoassinado na cadeia de certificados (e erros semelhantes)

Normalmente, os erros de certificado ocorrem em uma das seguintes situações:

- O aplicativo é conectado por meio de um _proxy transparente_. Isso significa que um servidor (como o servidor da empresa) está interceptando o tráfego HTTPS, descriptografando-o e, em seguida, criptografando-o usando um certificado autoassinado.
- Você está executando um aplicativo que está injetando um certificado TLS/SSL autoassinado nas mensagens HTTPS recebidas. Exemplos de aplicativos que inserem certificados incluem software antivírus e de inspeção de tráfego de rede.

Quando Gerenciador de Armazenamento vê um certificado autoassinado ou não confiável, ele não sabe mais se a mensagem HTTPS recebida foi alterada. Se você tiver uma cópia do certificado autoassinado, poderá instruir Gerenciador de Armazenamento a confiar, seguindo estas etapas:

1. Obtenha uma cópia X. 509 (. cer) codificada em base-64 do certificado.
2. Vá para **Editar** certificados  >  **SSL**  >  **importar certificados** e, em seguida, use o seletor de arquivos para localizar, selecionar e abrir o arquivo. cer.

Esse problema também pode ocorrer se houver vários certificados (raiz e intermediário). Para corrigir esse erro, ambos os certificados devem ser adicionados.

Se você não tiver certeza de onde o certificado provém, siga estas etapas para encontrá-lo:

1. Instalar OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): qualquer uma das versões leves deve ser suficiente.
    * Mac e Linux: devem ser incluídos no seu sistema operacional.
2. Execute o OpenSSL.
    * Windows: Abra o diretório de instalação, selecione **/bin/** e clique duas vezes em **openssl.exe**.
    * Mac e Linux: executar `openssl` de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`.
4. Procurar certificados autoassinados. Se você não tiver certeza de quais certificados são autoassinados, anote em qualquer lugar que o assunto `("s:")` e o emissor `("i:")` sejam os mesmos.
5. Quando você encontrar certificados autoassinados, para cada um, copie e cole tudo de (e incluindo) `-----BEGIN CERTIFICATE-----` por meio de `-----END CERTIFICATE-----` um novo arquivo. cer.
6. Abra Gerenciador de armazenamento e acesse **Editar** certificados  >  **SSL**  >  **importar certificados**. Em seguida, use o seletor de arquivos para localizar, selecionar e abrir os arquivos. cer que você criou.

Se você não encontrar certificados autoassinados seguindo estas etapas, entre em contato conosco por meio da ferramenta de comentários. Você também pode abrir Gerenciador de Armazenamento na linha de comando usando o `--ignore-certificate-errors` sinalizador. Quando aberto com esse sinalizador, Gerenciador de Armazenamento ignora erros de certificado.

## <a name="sign-in-issues"></a>Problemas de credenciais

### <a name="blank-sign-in-dialog-box"></a>Caixa de diálogo de entrada em branco

Caixas de diálogo de entrada em branco ocorrem com mais frequência quando Serviços de Federação do Active Directory (AD FS) (AD FS) solicita Gerenciador de Armazenamento para executar um redirecionamento, o que não é suportado pelo de todos os. Para contornar esse problema, você pode tentar usar o fluxo de código do dispositivo para entrar. Para fazer isso, siga estas etapas:

1. Na barra de ferramentas vertical esquerda, abra **configurações**. No painel configurações, vá para   >  **entrada do** aplicativo. Habilitar **usar entrada de fluxo de código de dispositivo**.
2. Abra a caixa de diálogo **conectar** (por meio do ícone de plugue na barra vertical do lado esquerdo ou selecionando **adicionar conta** no painel de conta).
3. Escolha o ambiente no qual você deseja entrar.
4. Selecione **Entrar.**
5. Siga as instruções no painel seguinte.

Se você não puder entrar na conta que deseja usar porque seu navegador padrão já está conectado a uma conta diferente, siga um destes procedimentos:

- Copiar manualmente o link e o código para uma sessão privada do seu navegador.
- Copiar manualmente o link e o código para um navegador diferente.

### <a name="reauthentication-loop-or-upn-change"></a>Loop de reautenticação ou alteração de UPN

Se você estiver em um loop de reautenticação ou tiver alterado o UPN de uma de suas contas, siga estas etapas:

1. Remova todas as contas e, em seguida, feche Gerenciador de Armazenamento.
2. Exclua a pasta .IdentityService do seu computador. No Windows, a pasta está localizada em `C:\users\<username>\AppData\Local`. Para Mac e Linux, você pode encontrar a pasta na raiz do seu diretório de usuário.
3. Se você estiver executando o Mac ou o Linux, também precisará excluir a entrada Microsoft. Developer. IdentityService do repositório de chaves do seu sistema operacional. No Mac, o keystore é o aplicativo de conjunto de *chaves GNOME* . No Linux, o aplicativo é normalmente chamado de _token_ de entrada, mas o nome pode ser diferente dependendo da sua distribuição.

### <a name="conditional-access"></a>Acesso condicional

Devido a uma limitação na biblioteca do Azure AD usada pelo Gerenciador de Armazenamento, o acesso condicional não tem suporte quando Gerenciador de Armazenamento está sendo usado no Windows 10, Linux ou macOS.

## <a name="mac-keychain-errors"></a>Erros de conjunto de chaves do Mac

Às vezes, o conjunto de chaves macOS pode inserir um estado que causa problemas para a biblioteca de autenticação Gerenciador de Armazenamento. Para obter o conjunto de chaves desse Estado, siga estas etapas:

1. Feche o Gerenciador de Armazenamento.
2. Abra o conjunto de chaves (pressione Command + barra de espaços, digite **keychain** e pressione Enter).
3. Selecione o conjunto de chaves "logon".
4. Selecione o ícone de cadeado para bloquear o conjunto de chaves. (O cadeado aparecerá bloqueado quando o processo for concluído. Pode levar alguns segundos, dependendo de quais aplicativos você abriu).

    ![Ícone de cadeado](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Abra o Gerenciador de Armazenamento.
6. Você receberá uma mensagem como "o Hub de serviço deseja acessar o conjunto de chaves". Insira sua senha da conta de administrador do Mac e selecione **sempre permitir** (ou **permitir** se **sempre permitir** não estiver disponível).
7. Tente entrar.

### <a name="general-sign-in-troubleshooting-steps"></a>Etapas gerais de solução de problemas de entrada

* Se você estiver no macOS e a janela de entrada nunca aparecer na caixa de diálogo **aguardando autenticação** , tente [estas etapas](#mac-keychain-errors).
* Reinicie Gerenciador de Armazenamento.
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Verifique se as configurações de proxy e certificado estão configuradas corretamente para seu computador e Gerenciador de Armazenamento.
* Se você estiver executando o Windows e tiver acesso ao Visual Studio 2019 no mesmo computador e às credenciais de entrada, tente entrar no Visual Studio 2019. Após uma entrada bem-sucedida no Visual Studio 2019, você pode abrir Gerenciador de Armazenamento e ver sua conta no painel de conta.

Se nenhum desses métodos funcionar, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Assinaturas ausentes e locatários desfeitos

Se você não puder recuperar suas assinaturas depois de entrar com êxito, tente os seguintes métodos de solução de problemas:

* Verifique se sua conta tem acesso às assinaturas que você espera. Você pode verificar seu acesso entrando no portal para o ambiente do Azure que você está tentando usar.
* Verifique se você entrou por meio do ambiente correto do Azure (Azure, Azure China 21Vianet, Azure Alemanha, governo dos EUA do Azure ou ambiente personalizado).
* Se você estiver protegido por um servidor proxy, verifique se você configurou o Gerenciador de Armazenamento proxy corretamente.
* Tente remover e adicionar novamente a conta.
* Se houver um link "mais informações", verifique quais mensagens de erro estão sendo relatadas para os locatários que estão falhando. Se você não tiver certeza de como responder às mensagens de erro, fique à vontade para [abrir um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Não é possível remover uma conta anexada ou um recurso de armazenamento

Se não for possível remover uma conta anexada ou um recurso de armazenamento por meio da interface do usuário, você poderá excluir manualmente todos os recursos anexados excluindo as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS - `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Feche Gerenciador de Armazenamento antes de excluir essas pastas.

> [!NOTE]
> Se você já importou qualquer certificado SSL, faça backup do conteúdo do `certs` diretório. Posteriormente, você pode usar o backup para reimportar os certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

O Gerenciador de Armazenamento dá suporte à conexão com recursos de armazenamento do Azure por meio de um servidor proxy. Se você tiver problemas para se conectar ao Azure por meio do proxy, aqui estão algumas sugestões.

> [!NOTE]
> Gerenciador de Armazenamento dá suporte apenas à autenticação básica com servidores proxy. Não há suporte para outros métodos de autenticação, como NTLM.

> [!NOTE]
> Gerenciador de Armazenamento não dá suporte a arquivos de configuração automática de proxy para definir as configurações de proxy.

### <a name="verify-storage-explorer-proxy-settings"></a>Verificar Gerenciador de Armazenamento configurações de proxy

O parâmetro **Application → proxy → de configuração de proxy** determina a origem da qual Gerenciador de armazenamento Obtém a configuração de proxy.

Se você selecionar "usar variáveis de ambiente", certifique-se de definir as `HTTPS_PROXY` variáveis de ambiente ou (as variáveis de ambiente diferenciam `HTTP_PROXY` maiúsculas de minúsculas, portanto, certifique-se de definir as variáveis corretas). Se essas variáveis forem indefinidas ou inválidas, Gerenciador de Armazenamento não usará um proxy. Reinicie Gerenciador de Armazenamento depois de modificar as variáveis de ambiente.

Se você selecionar "usar configurações de proxy de aplicativo", verifique se as configurações de proxy no aplicativo estão corretas.

### <a name="steps-for-diagnosing-issues"></a>Etapas para diagnosticar problemas

Se você ainda tiver problemas, Experimente estes métodos de solução de problemas:

1. Se você puder se conectar à Internet sem usar o proxy, verifique se Gerenciador de Armazenamento funciona sem as configurações de proxy habilitadas. Se Gerenciador de Armazenamento se conectar com êxito, pode haver um problema com o servidor proxy. Trabalhe com o administrador para identificar os problemas.
2. Verifique se outros aplicativos que usam o servidor proxy funcionam conforme o esperado.
3. Verifique se você pode se conectar ao portal para o ambiente do Azure que você está tentando usar.
4. Verifique se que você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, deverá receber uma `InvalidQueryParameterValue` resposta XML ou semelhante.
5. Verifique se outra pessoa que usa Gerenciador de Armazenamento com o mesmo servidor proxy pode se conectar. Se puderem, talvez você precise entrar em contato com o administrador do servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Uma ferramenta de rede, como o Fiddler, pode ajudá-lo a diagnosticar problemas.

1. Configure sua ferramenta de rede como um servidor proxy em execução no host local. Se você precisar continuar trabalhando atrás de um proxy real, talvez seja necessário configurar sua ferramenta de rede para se conectar por meio do proxy.
2. Verifique o número da porta usado por sua ferramenta de rede.
3. Defina Gerenciador de Armazenamento configurações de proxy para usar o host local e o número da porta da ferramenta de rede (como "localhost: 8888").
 
Quando definido corretamente, sua ferramenta de rede registrará solicitações de rede feitas por Gerenciador de Armazenamento para pontos de extremidade de serviço e gerenciamento.
 
Se a sua ferramenta de rede não parece estar registrando Gerenciador de Armazenamento tráfego, tente testar sua ferramenta com um aplicativo diferente. Por exemplo, digite a URL do ponto de extremidade para um dos seus recursos de armazenamento (como `https://contoso.blob.core.windows.net/` ) em um navegador da Web e você receberá uma resposta semelhante a:

  ![Exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  A resposta sugere que o recurso existe, embora você não possa acessá-lo.

Se a sua ferramenta de rede mostrar apenas o tráfego de outros aplicativos, talvez seja necessário ajustar as configurações de proxy no Gerenciador de Armazenamento. Caso contrário, você fez a necessidade de ajustar as configurações da sua ferramenta.

### <a name="contact-proxy-server-admin"></a>Entre em contato com o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, talvez seja necessário contatar o administrador do servidor proxy para:

* Verifique se o proxy não bloqueia o tráfego para os pontos de extremidade de gerenciamento ou recurso do Azure.
* Verifique o protocolo de autenticação usado por seu servidor proxy. Gerenciador de Armazenamento só dá suporte a protocolos de autenticação básica. Gerenciador de Armazenamento não dá suporte a proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é Possível Recuperar Filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações de proxy estão corretas.

Se o proprietário de uma assinatura ou conta tiver concedido acesso a um recurso, verifique se você tem permissões de leitura ou de lista para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A cadeia de conexão não tem definições de configuração completas

Se você receber essa mensagem de erro, é possível que você não tenha as permissões necessárias para obter as chaves para sua conta de armazenamento. Para confirmar que esse é o caso, vá para o portal e localize sua conta de armazenamento. Você pode fazer isso clicando com o botão direito do mouse no nó da sua conta de armazenamento e selecionando **abrir no portal**. Em seguida, vá para a folha **chaves de acesso** . Se você não tiver permissões para exibir chaves, verá uma mensagem "você não tem acesso". Para contornar esse problema, você pode obter a chave de conta de outra pessoa e anexar por meio do nome e da chave, ou pode pedir a alguém uma SAS para a conta de armazenamento e usá-la para anexar a conta de armazenamento.

Se você vir as chaves de conta, execute um problema no GitHub para que possamos ajudá-lo a resolver o problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Ocorreu um erro ao adicionar uma nova conexão: TypeError: não é possível ler a propriedade ' version ' de undefined

Se você receber essa mensagem de erro ao tentar adicionar uma conexão personalizada, os dados de conexão armazenados no Gerenciador de credenciais local poderão estar corrompidos. Para contornar esse problema, tente excluir as conexões locais corrompidas e, em seguida, adicione-as novamente:

1. Iniciar Gerenciador de Armazenamento. No menu, vá para **ajuda**  >  **alternar ferramentas para desenvolvedores**.
2. Na janela aberta, na guia **aplicativo** , vá para **armazenamento local** (lado esquerdo) > **file://**.
3. Dependendo do tipo de conexão com a qual você está tendo um problema, procure sua chave e copie seu valor em um editor de texto. O valor é uma matriz de seus nomes de conexão personalizados, como o seguinte:
    * Contas de armazenamento
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Contêineres de blobs
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Compartilhamentos de arquivo
        * `StorageExplorer_CustomConnections_Files_v1`
    * Filas
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelas
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Depois de salvar os nomes de conexão atuais, defina o valor em Ferramentas para Desenvolvedores como `[]` .

Se desejar preservar as conexões que não estão corrompidas, você poderá usar as etapas a seguir para localizar as conexões corrompidas. Se você não se lembrar de perder todas as conexões existentes, poderá ignorar essas etapas e seguir as instruções específicas da plataforma para limpar os dados de conexão.

1. Em um editor de texto, adicione novamente cada nome de conexão a Ferramentas para Desenvolvedores e, em seguida, verifique se a conexão ainda está funcionando.
2. Se uma conexão estiver funcionando corretamente, ela não estará corrompida e você poderá deixá-la lá com segurança. Se uma conexão não estiver funcionando, remova seu valor de Ferramentas para Desenvolvedores e registre-o para que você possa adicioná-lo novamente mais tarde.
3. Repita até que você tenha examinado todas as suas conexões.

Depois de passar por todas as suas conexões, para todos os nomes de conexões que não forem adicionados de volta, você deverá limpar os dados corrompidos (se houver) e adicioná-los novamente usando as etapas padrão no Gerenciador de Armazenamento:

# <a name="windows"></a>[Windows](#tab/Windows)

1. No menu **Iniciar** , pesquise por **Gerenciador de credenciais** e abra-o.
2. Vá para **credenciais do Windows**.
3. Em **credenciais genéricas**, procure entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Exclua essas entradas e adicione novamente as conexões.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Abra o Spotlight (Command + barra de espaços) e pesquise por acesso ao conjunto de **chaves**.
2. Procure entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Exclua essas entradas e adicione novamente as conexões.

# <a name="linux"></a>[Linux](#tab/Linux)

O gerenciamento de credenciais locais varia dependendo da distribuição do Linux. Se sua distribuição do Linux não fornecer uma ferramenta interna de GUI para o gerenciamento de credenciais local, você poderá instalar uma ferramenta de terceiros para gerenciar suas credenciais locais. Por exemplo, você pode usar o [macavalo](https://wiki.gnome.org/Apps/Seahorse/), uma ferramenta de GUI de software livre para gerenciar as credenciais locais do Linux.

1. Abra sua ferramenta de gerenciamento de credenciais local e localize suas credenciais salvas.
2. Procure entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Exclua essas entradas e adicione novamente as conexões.
---

Se você ainda encontrar esse erro depois de executar essas etapas, ou se quiser compartilhar o que você suspeita que danificou as conexões, [abra um problema](https://github.com/microsoft/AzureStorageExplorer/issues) em nossa página do github.

## <a name="issues-with-sas-url"></a>Problemas com a URL SAS

Se você estiver se conectando a um serviço por meio de uma URL SAS e ocorrendo um erro:

* Verifique se a URL fornece as permissões necessárias para ler ou lista recursos.
* Verifique se a URL não expirou.
* Se a URL SAS tiver base em uma política de acesso, verifique se a política de acesso não foi revogada.

Se você anexou acidentalmente usando uma URL SAS inválida e agora não puder desanexar, siga estas etapas:

1. Quando estiver executando Gerenciador de Armazenamento, pressione F12 para abrir a janela Ferramentas para Desenvolvedores.
2. Na guia **aplicativo** , selecione **armazenamento local**  >  **file://** na árvore à esquerda.
3. Localize a chave associada ao tipo de serviço do URI SAS problemático. Por exemplo, se o URI da SAS incorreta for para um contêiner de blob, procure a chave nomeada `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. O valor da chave deve ser uma matriz JSON. Localize o objeto associado ao URI inadequado e, em seguida, exclua-o.
5. Pressione Ctrl+R para recarregar o Gerenciador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências do Linux

### <a name="snap"></a>Snap

Gerenciador de Armazenamento 1.10.0 e posterior está disponível como um snap do repositório de snap. O Gerenciador de Armazenamento snap instala todas as suas dependências automaticamente e é atualizado quando uma nova versão do snap está disponível. Instalar o Gerenciador de Armazenamento snap é o método recomendado de instalação.

Gerenciador de Armazenamento requer o uso de um Gerenciador de senhas, que talvez você precise se conectar manualmente antes que Gerenciador de Armazenamento funcione corretamente. Você pode se conectar Gerenciador de Armazenamento ao Gerenciador de senhas do sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Arquivo. tar. gz

Você também pode baixar o aplicativo como um arquivo. tar. gz, mas precisará instalar as dependências manualmente.

Gerenciador de Armazenamento conforme fornecido no download. tar. gz tem suporte apenas nas seguintes versões do Ubuntu. Gerenciador de Armazenamento pode funcionar em outras distribuições do Linux, mas não têm suporte oficial.

- Ubuntu 20, 4 x64
- Ubuntu 18, 4 x64
- Ubuntu 16, 4 x64

Gerenciador de Armazenamento requer que o .NET Core seja instalado em seu sistema. Recomendamos o .NET Core 2,1, mas Gerenciador de Armazenamento também funcionará com 2,2.

> [!NOTE]
> Gerenciador de Armazenamento versão 1.7.0 e anteriores exigem o .NET Core 2,0. Se você tiver uma versão mais recente do .NET Core instalada, terá que [corrigir Gerenciador de armazenamento](#patching-storage-explorer-for-newer-versions-of-net-core). Se você estiver executando o Gerenciador de Armazenamento 1.8.0 ou posterior, será necessário pelo menos o .NET Core 2,1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Baixe o arquivo Gerenciador de Armazenamento. tar. gz.
2. Instale o [tempo de execução do .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Baixe o arquivo Gerenciador de Armazenamento. tar. gz.
2. Instale o [tempo de execução do .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Baixe o arquivo Gerenciador de Armazenamento. tar. gz.
2. Instale o [tempo de execução do .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Muitas bibliotecas exigidas pelo Gerenciador de Armazenamento vêm pré-instalados com as instalações padrão da Canonical do Ubuntu. Ambientes personalizados podem não ter algumas dessas bibliotecas. Se você tiver problemas ao iniciar o Gerenciador de Armazenamento, é recomendável verificar se os seguintes pacotes estão instalados em seu sistema:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Aplicação de patch Gerenciador de Armazenamento para versões mais recentes do .NET Core

Para Gerenciador de Armazenamento 1.7.0 ou anterior, talvez seja necessário corrigir a versão do .NET Core usada pelo Gerenciador de Armazenamento:

1. Baixe a versão 1.5.43 do StreamJsonRpc [do NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "baixar pacote" no lado direito da página.
2. Depois de baixar o pacote, altere sua extensão de arquivo de `.nupkg` para `.zip` .
3. Descompacte o pacote.
4. Abra a pasta `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copie `StreamJsonRpc.dll` para os seguintes locais na pasta gerenciador de armazenamento:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Abrir no Explorer" da portal do Azure não funciona

Se o botão **abrir no Explorer** na portal do Azure não funcionar, verifique se você está usando um navegador compatível. Os seguintes navegadores foram testados quanto à compatibilidade:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Próximas etapas

Se nenhuma dessas soluções funcionar para você, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Você também pode fazer isso selecionando o botão **relatar o problema no GitHub** no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)