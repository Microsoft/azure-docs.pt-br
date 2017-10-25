---
title: Configurar o Azure MFA| Microsoft Docs
description: "Esta é a página da Autenticação Multifator do Azure que descreve o que fazer depois com o MFA.  Isso inclui relatórios, alerta de fraude, desvio único, mensagens de voz personalizadas, cache, senhas de ips e aplicativos confiáveis."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: kgremban
ms.openlocfilehash: 9d77b9329116afcf2fdde48d672c95020738138c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar a Autenticação Multifator do Azure
Este artigo ajuda a gerenciar o Autenticação Multifator do Azure, agora que tudo está funcionando.  Ele aborda diversos tópicos que ajudarão você a aproveitar ao máximo a Autenticação Multifator do Azure.  Alguns desses recursos não estão disponíveis em todas as versões da Autenticação Multifator do Azure.

| Recurso | Descrição | 
|:--- |:--- |
| [Alerta de fraude](#fraud-alert) |O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. |
| [Desvio único](#one-time-bypass) |Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator. |
| [Mensagens de voz personalizadas](#custom-voice-messages) |As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator. |
| [Cache](#caching-in-azure-multi-factor-authentication) |O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. |
| [IPs Confiáveis](#trusted-ips) |Os administradores de um locatário gerenciado ou federado podem usar os IPs Confiáveis para ignorar a verificação em duas etapas de usuários que se conectam pela intranet local da empresa. |
| [Senhas de aplicativo](#app-passwords) |A senha de aplicativo permite que um aplicativo que não tenha reconhecimento de MFA possa se desviar da autenticação multifator e continuar trabalhando. |
| [Lembrar da Autenticação Multifator para dispositivos e navegadores lembrados](#remember-multi-factor-authentication-for-devices-that-users-trust) |Permite lembrar dispositivos por um número de dias específico após a entrada bem-sucedida de um usuário usando o MFA. |
| [Métodos de verificação selecionáveis](#selectable-verification-methods) |Permite que você escolha os métodos de autenticação disponíveis para os usuários usarem. |

## <a name="access-the-azure-mfa-management-portal"></a>Acesse o Portal de Gerenciamento do Azure MFA

Os recursos abordados neste artigo são configurados no Portal de Gerenciamento da Autenticação Multifator. Há duas maneiras de acessar o Portal de Gerenciamento do MFA no portal clássico do Azure. A primeira é usando um provedor de autenticação multifator. A segunda é por configurações do serviço MFA. 

### <a name="use-an-authentication-provider"></a>Usar um provedor de autenticação

Se você usar um provedor de autenticação multifator para uma MFA com base em consumo, utilize este método para acessar o portal de gerenciamento.

Para acessar o Portal de Gerenciamento do MFA usando um provedor de autorização multifator do Azure, acesse o portal clássico do Azure como administrador e selecione a opção Active Directory. Clique na guia **Provedores de Autenticação Multifator**, selecione o diretório e clique no botão **Gerenciar** na parte inferior.

### <a name="use-the-mfa-service-settings-page"></a>Usar a página Configurações do Serviço do MFA 

Caso tenha uma das seguintes licenças, você poderá usar a página Configurações do Serviço do MFA:
- MFA do Azure
- AD Premium do Azure 
- Enterprise Mobility + Security

Para acessar o Portal de Gerenciamento do MFA pela página Configurações do Serviço MFA, acesse o portal clássico do Azure como administrador e selecione a opção Active Directory. Clique em seu diretório e depois na guia **Configurar** . Na seção da autenticação multifator, escolha **Gerenciar configurações de serviço**. Na parte inferior da página Configurações do Serviço do MFA, clique no link **Ir para o portal** .


## <a name="fraud-alert"></a>Alerta de fraude
O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos.  Os usuários podem relatar fraude com o aplicativo móvel ou pelo telefone.

### <a name="set-up-fraud-alert"></a>Configurar o alerta de fraude
1. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
2. No Portal de Gerenciamento da Autenticação Multifator do Azure, clique em **Configurações** na seção Configurar.
3. Na seção Alerta de Fraude da página Configurações, maque a caixa de seleção **Permitir que os usuários enviem alertas de fraudes**.
4. Selecione **Salvar** para salvar suas alterações. 

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear usuário quando fraude for relatada** - se de um usuário relatar uma fraude, sua conta será bloqueada.
- **Código para relatar fraude durante a saudação inicial** - os usuários normalmente pressionam # para confirmar a verificação em duas etapas. Se quiserem relatar uma fraude, eles devem inserir um código antes de pressionar #. Esse código é **0** por padrão, mas você pode personalizá-lo.

> [!NOTE]
> As saudações de voz padrão da Microsoft instruem os usuários a pressionar 0# para enviar um alerta de fraude. Se você quiser usar um código diferente de 0, deverá gravar e carregar suas próprias saudações de voz personalizadas com instruções apropriadas.

![Opções de alerta de fraude - captura de tela](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Como os usuários podem relatar fraudes 
O alerta de fraude pode ser informado de duas maneiras.  Seja por meio do aplicativo móvel ou telefone.  

#### <a name="report-fraud-with-the-mobile-app"></a>Relatar fraudes com o aplicativo móvel
1. Quando você receber uma verificação em seu telefone, selecione-a para iniciar o aplicativo Microsoft Authenticator.
2. Selecione **Negar** na notificação. 
3. Clique em **Relatar fraude**.
4. Feche o aplicativo.

#### <a name="report-fraud-with-a-phone"></a>Relatar fraude com um telefone
1. Ao receber uma chamada de verificação em seu telefone, atenda-a.  
2. Para relatar fraudes, insira o código de fraude (o padrão é 0) e, em seguida, pressione #. Você será notificado de que um alerta de fraude foi enviado.
3. Encerre a chamada.

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione **Provedores de autenticação multifator** para mostrar uma lista de seus Provedores de Autenticação Multifator.
4. Selecione o provedor de autenticação multifator e clique em **Gerenciar** na parte inferior da página. O Portal de Gerenciamento da Autenticação Multifator do Azure é aberto.
5. No Portal de Gerenciamento da Autenticação Multifator do Azure, em Exibir um relatório, clique em **Alerta de fraude**.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários, números de telefone e o status de usuários.
7. Clique em **Executar** para mostrar um relatório de alertas de fraude. Clique em **Exportar para CSV** se quiser exportar o relatório.

## <a name="one-time-bypass"></a>Desvio único
Um bypass avulso permite que um usuário se autentique uma única vez ao sem executar uma verificação em duas etapas. O bypass é temporário e expira após um número de segundos especificado. Quando o aplicativo móvel ou o telefone não estiver recebendo notificações ou chamadas telefônicas, você poderá habilitar um bypass avulso para que o usuário possa acessar o recurso desejado.

### <a name="create-a-one-time-bypass"></a>Criar um bypass avulso
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3. Se você visualizar o nome do seu Provedor de MFA do Azure à esquerda com um **+** ao lado dele, clique no **+**. Grupos de replicação diferentes do Servidor MFA e o grupo Padrão do Azure são mostrados. Selecione o grupo apropriado.
4. Em Administração do Usuário, clique em **Bypass Avulso**.
5. Na página de Bypass Único, clique em **Novo Bypass Único**.

  ![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)

6. Insira o nome de usuário, a duração do bypass e o motivo do bypass. Clique em **Bypass**.
7. Como o limite de tempo começa a contar imediatamente, o usuário precisa entrar antes que o bypass avulso expire. 

### <a name="view-the-one-time-bypass-report"></a>Para exibir o relatório de bypass avulso
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione **Provedores de autenticação multifator** para mostrar uma lista de seus Provedores de Autenticação Multifator.
4. Selecione o provedor de autenticação multifator e clique em **Gerenciar** na parte inferior da página. O Portal de Gerenciamento da Autenticação Multifator do Azure é aberto.
5. No Portal de Gerenciamento da Autenticação Multifator do Azure, à esquerda, em Exibir um relatório, clique em **Bypass Avulso**.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários, números de telefone e o status de usuários.
7. Clique em **Executar** para mostrar um relatório de bypasses. Clique em **Exportar para CSV** se quiser exportar o relatório.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a verificação em duas etapas. É possível usar mensagens de voz personalizadas adicionalmente ou substituir os registros da Microsoft.

Antes de começar, esteja ciente das seguintes informações:

* Os formatos de arquivo compatíveis são .wav e .mp3.
* O limite de tamanho de arquivo é de 5 MB.
* As mensagens de autenticação devem ter menos de 20 segundos. As mensagens com mais de 20 segundos não dão aos usuários tempo suficiente para responder antes que o tempo de verificação se esgote.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

Há duas etapas para criar uma mensagem personalizada. Primeiro, carregue a mensagem e, em seguida, ative-a para seus usuários.

Para carregar uma mensagem personalizada:

1. Crie uma mensagem de voz personalizada usando um dos formatos de arquivo compatíveis.
2. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
3. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
4. No Portal de Gerenciamento da Autenticação Multifator do Azure, clique em **Mensagens de Voz** na seção Configurar.
5. Na página Configurar: Mensagens de Voz, clique em **Nova Mensagem de Voz**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom1.png)
6. Na página Configurar: Novas Mensagens de Voz, clique em **Gerenciar Arquivos de Som**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom2.png)
7. Na página Configurar: Arquivos de Som, clique em **Carregar Arquivo de Som**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom3.png)
8. Na página Configurar: Carregar Arquivo de Som, clique em **Procurar** e navegue até a mensagem de voz e clique em **Abrir**.
9. Adicione uma Descrição e clique em **Carregar**.
10. Na conclusão do upload da mensagem de voz, uma mensagem confirma que você fez upload do arquivo com êxito.

Para ativar a mensagem para seus usuários:

1. À esquerda, clique em **Mensagens de Voz**.
2. Na seção Mensagens de voz, clique em **Nova mensagem de voz**.
3. No menu suspenso Idioma, selecione um idioma.
4. Se esta mensagem for para um aplicativo específico, especifique-o na caixa de Aplicativo.
5. No menu suspenso Tipo de Mensagem, selecione o tipo de mensagem que será substituído pela nova mensagem personalizada.
6. No menu suspenso Arquivo de Som, selecione o arquivo de som que você carregou na primeira parte.
7. Clique em **Criar**. Uma mensagem confirma que você criou com êxito uma mensagem de voz.
    ![Nuvem](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Cache na Autenticação Multifator do Azure
O cache permite que você defina um período específico para que as próximas tentativas de autenticação subsequentes nesse período automaticamente tenham êxito. O cache é usado quando os sistemas locais, como a VPN, enviam muitas solicitações de verificação enquanto a primeira solicitação ainda está em andamento, permitindo que as próximas solicitações tenham êxito automaticamente depois que o usuário é bem-sucedido na primeira verificação em andamento. 

Observe que o cache não deve ser usado para acessar o Azure AD.

### <a name="set-up-caching"></a>Configurar o cache 
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3. No Portal de Gerenciamento da Autenticação Multifator do Azure, clique em **Cache** na seção Configurar.
4. Clique em **Novo cache** na página Configurar cache.
5. Selecione o tipo de Cache e os segundos de cache. Clique em **Criar**.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IPs confiáveis
Os IPs Confiáveis são um recurso da MFA do Azure que os administradores de um locatário gerenciado ou federado podem usar para ignorar a verificação em duas etapas. Eles também são usados por usuários que estão entrando da intranet local da empresa. Esse recurso está disponível na versão completa da Autenticação Multifator do Azure, não na versão gratuita para administradores. Para saber como obter a versão completa da Autenticação Multifator do Azure, consulte [Autenticação Multifator do Azure](multi-factor-authentication.md).

| Tipo de locatário do Azure AD | Opções disponíveis de IPs confiáveis |
|:--- |:--- |
| Gerenciada |<li>Intervalos de endereços IP específicos – os administradores podem definir um intervalo de endereços IP que tem permissão para ignorar a autenticação multifator de usuários que se conectam pela intranet da empresa.</li> |
| Federado |<li>Todos os usuários federados - todos os usuários federados que estiverem fazendo logon de dentro da organização ignorarão a verificação em duas etapas usando uma declaração emitida pelo AD FS.</li><br><li>Intervalos de endereços IP específicos – os administradores podem definir um intervalo de endereços IP que tem permissão para ignorar a autenticação multifator de usuários que se conectam pela intranet da empresa. |

Esse desvio só funciona dentro da intranet da empresa. 

**Experiência do usuário final dentro da rede corporativa:**

Quando o recurso IPs confiáveis estiver desabilitado, será necessário utilizar a verificação em duas etapas para fluxos de navegador e senhas de aplicativo para antigos aplicativos clientes avançados. 

Quando o recurso IPs Confiáveis estiver habilitado, a verificação em duas etapas *não* será obrigatória para fluxos do navegador. As senhas de aplicativo *não* são necessárias para aplicativos cliente avançados mais antigos, desde que o usuário ainda não tenha criado uma senha de aplicativo. Se uma senha de aplicativo for usada, ela será necessária. 

**Experiência do usuário final fora da rede corporativa:**

Não importa se o recurso IPs confiáveis está habilitado ou não, será necessário utilizar a verificação em duas etapas para fluxos de navegador e senhas de aplicativo para antigos aplicativos clientes avançados. 

### <a name="to-enable-trusted-ips"></a>Para habilitar IPs confiáveis
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até a página de configurações do serviço MFA de acordo com as instruções fornecidas no início deste artigo.
3. Na página Configurações do Serviço, em IPs Confiáveis, há duas opções:
   
   * **Para solicitações de usuários federados originárias da minha intranet** – marque a caixa de seleção. Todos os usuários federados que estiverem entrando pela rede corporativa ignorarão a verificação em duas etapas usando uma declaração emitida pelo AD FS.
   * **Para solicitações de um intervalo específico de IPs públicos** – insira os endereços IP nas caixas fornecidas usando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP no intervalo xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Você pode inserir até 50 intervalos de endereços IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.
4. Clique em **Salvar**.
5. Depois que as atualizações forem aplicadas, clique em **Fechar**.

![IPs confiáveis](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Senhas de aplicativo
Alguns aplicativos, como o Office 2010 ou anterior e o Apple Mail, não oferecem suporte à verificação em duas etapas. Eles não estão configurados para aceitar uma segunda verificação. Para usar esses aplicativos, você precisará usar as "senhas de aplicativo" no lugar de sua senha tradicional. A senha de aplicativo permite que um aplicativo ignore a verificação em duas etapas e continue funcionando.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo o Outlook) e mais recente são compatíveis com protocolos da autenticação moderna e podem ser usados com a verificação em duas etapas. Depois de habilitada, não será mais necessário fornecer as senhas de aplicativo para esses clientes.  Para saber mais, veja [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Coisas importantes a saber sobre senhas de aplicativo
Veja a seguir uma lista de itens importantes que você deve conhecer sobre senhas de aplicativo.

* As senhas de aplicativo devem ser inseridas na caixa de entrada, uma vez por aplicativo. Os usuários não precisam decorá-las e fornecê-las toda vez.
* A senha real é gerada automaticamente e não é fornecida pelo usuário. A senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Há um limite de 40 senhas por usuário. 
* Os aplicativos que armazenam senhas em cache e a usam em cenários locais podem começar a falhar, pois a senha do aplicativo não será conhecida fora da ID organizacional. Um exemplo é o de emails do Exchange que estão no local, mas o email arquivado está na nuvem. A mesma senha não funciona.
* Quando a autenticação multifator é iniciada, você pode usar sua senha com alguns clientes que não usam navegador. Não é possível usar senhas de aplicativo para tarefas administrativas. Quando você criar uma conta de serviço, atribua a ela uma senha forte para executar scripts do PowerShell e não habilite essa conta para a verificação em duas etapas.

> [!WARNING]
> As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. As senhas de domínio são necessárias para autenticação local e as senhas de aplicativo são necessárias para autenticação na nuvem.

### <a name="naming-guidance-for-app-passwords"></a>Nomeando orientação para senhas de aplicativo
Os nomes das senhas de aplicativo devem refletir o dispositivo no qual as senhas serão usadas. Por exemplo, se você tiver um laptop que tenha aplicativos que não usam navegador, crie uma senha de aplicativo chamada Laptop e use essa senha de aplicativo. Em seguida, crie outra senha de aplicativo chamada Desktop para os mesmos aplicativos em um computador desktop. 

A Microsoft recomenda criar uma senha de aplicativo por dispositivo, e não uma senha de aplicativo por aplicativo.

### <a name="federated-sso-app-passwords"></a>Senhas de aplicativo federado (SSO)
O Azure AD oferece suporte à federação (logon único) com os Serviços de Domínio do Active Directory Domain Services (AD DS) locais do Windows Server. Se sua organização for federada com o Azure AD e você quiser usar a Autenticação Multifator do Azure, é importante saber sobre as senhas de aplicativo. Esta seção se aplica apenas aos clientes federados.

* As senhas de aplicativo são verificadas Azure AD e, portanto, ignoram a federação. A federação só é usada ativamente na configuração das senhas de aplicativo.
* Para usuários federados (SSO), nós nunca consultamos o Provedor de Identidade (IdP), diferentemente do fluxo passivo. As senhas são armazenadas na ID organizacional. Se o usuário sair da empresa, essa informação deverá ir para a ID organizacional usando o DirSync em tempo real. A desabilitação/exclusão da conta pode levar até 3 horas para sincronizar, atrasando a desabilitação/exclusão da Senha de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela senha de aplicativo.
* Nenhum recurso de registro de autenticação/auditoria local está disponível para a Senha de aplicativo.
* Determinados designs arquitetônicos avançados podem exigir uma combinação de nome de usuário, senhas e senhas de aplicativo organizacionais ao usar a verificação em duas etapas com clientes. Embora isso dependa de onde eles se autenticam. Para clientes que se autenticam em uma infraestrutura local, você usaria um nome e senha da organização. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

  Por exemplo, suponha que você tenha uma arquitetura que consista nestas instâncias:

  * Você está associando sua instância local do Active Directory com o Azure AD
  * Você está usando o Exchange online
  * Você está usando o Lync que está especificamente no local
  * Você está usando o Azure Multi-Factor Authentication

  ![Prova](./media/multi-factor-authentication-whats-next/federated.png)

  Nessas instâncias, você deve seguir estas etapas:

  * Ao entrar no Lync, use o nome de usuário e a senha de suas organizações.
  * Ao tentar acessar o catálogo de endereços por meio de um cliente Outlook que se conecte ao Exchange online, use uma senha de aplicativo.

### <a name="allow-app-password-creation"></a>Permitir a criação de senha de aplicativo
Por padrão, os usuários não podem criar senhas de aplicativo, mas você pode habilitar o recurso. Para permitir que os usuários criem senhas de aplicativo, faça o seguinte:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até a página de configurações do serviço MFA de acordo com as instruções fornecidas no início deste artigo.
3. Selecione o botão de opção ao lado de **Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores**.

![Criar senhas de aplicativo](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Criar senhas de aplicativo
Os usuários podem criar senhas de aplicativo durante o registro inicial. Essa opção é oferecida a eles ao final do processo de registro, permitindo que eles criem senhas de aplicativo.

Os usuários também podem criar senhas de aplicativo após o registro. Eles podem criar as senhas de aplicativo alterando suas configurações no Portal do Azure ou no portal do Office 365. Para obter mais informações e etapas detalhadas para os usuários, consulte [O que são senhas de aplicativo na Autenticação Multifator do Azure](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Lembrar a autenticação multifator em dispositivos de confiança dos usuários
Lembrar Autenticação Multifator para dispositivos e navegadores de confiança dos usuários é um recurso gratuito para todos os usuários do MFA. A autenticação multifator permite aos usuários ignorar a MFA por um número definido de dias após a conexão. Esse recurso melhora a usabilidade, pois minimiza o número de vezes que um usuário executa a verificação em duas etapas no mesmo dispositivo.

No entanto, se um dispositivo ou a conta for comprometido, lembrar a autenticação multifator em dispositivos confiáveis pode colocar sua segurança em risco. Caso uma conta corporativa seja comprometida ou um dispositivo confiável seja perdido ou roubado, você precisará [restaurar a Autenticação Multifator em todos os dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Essa ação revoga o status de confiável de todos os dispositivos, e o usuário precisará executar a verificação em duas etapas novamente. Você também pode instruir seus usuários a restaurar o MFA em seus próprios dispositivos com as instruções em [Gerenciar as configurações de verificação em duas etapas](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Como ele funciona

Lembre-se que a autenticação multifator funciona com a definição de um cookie persistente no navegador quando um usuário seleciona a caixa "Não perguntar novamente por **X** dias" ao entrar. O usuário não será solicitado a executar a MFA novamente nesse navegador até que o cookie expire. Se o usuário abrir um navegador diferente no mesmo dispositivo ou limpar os cookies, ele será solicitado a verificar novamente. 

A caixa de seleção "Não perguntar novamente por **X** dias" não é exibida em aplicativos que não usam navegador, independentemente de eles suportarem a autenticação moderna ou não. Esses aplicativos usam tokens de atualização que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD confere se a última verificação em duas etapas foi realizada dentro do número de dias configurado. 

Em suma, lembre-se de que a MFA em dispositivos confiáveis reduz o número de autenticações em aplicativos Web (que normalmente sempre solicitam). Mas também aumenta o número de autenticações para clientes de autenticação moderna (que normalmente solicitam a cada 90 dias).

> [!NOTE]
>Esse recurso não é compatível com o recurso "Manter-me conectado" do AD FS quando os usuários executam uma verificação em duas etapas para o AD FS por meio do Servidor MFA do Azure ou uma solução MFA de terceiros. Se os usuários selecionarem "Manter-me conectado" no AD FS e também marcarem seus dispositivos como confiáveis para o MFA, eles não poderão verificar depois que o número de dias de "Lembrar do MFA" expirar. O Azure AD solicita uma nova verificação em duas etapas, mas o AD FS retorna um token com a declaração e a data originais do MFA em vez de realizar a verificação em duas etapas novamente. Esse processo aciona um loop de verificação entre o Azure AD e o AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar a opção Lembrar autenticação multifator
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até a página de configurações do serviço MFA de acordo com as instruções fornecidas no início deste artigo.
3. Na página Configurações do Serviço, em Gerenciar configurações do dispositivo do usuário, marque a caixa de seleção **Permitir que os usuários lembrem a autenticação multifator em dispositivos que eles confiam**.
   ![Lembrar dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
4. Defina o número de dias que os dispositivos confiáveis podem ignorar a verificação em duas etapas. O padrão é 14 dias.
5. Clique em **Salvar**.
6. Clique em **fechar**

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar esse recurso, os usuários poderão marcar um dispositivo como confiável quando fizerem logon e marcarem a opção **Não perguntar novamente**.

![Não perguntar novamente - captura de tela](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionáveis
Você pode escolher quais métodos de verificação estarão disponíveis para os usuários. A tabela a seguir fornece uma visão geral de cada método.

Quando os usuários registram suas contas na MFA, eles escolhem o método de verificação preferido das opções que você habilitou. As diretrizes para o processo de registro são abordadas em [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

| Método | Descrição |
|:--- |:--- |
| Ligue para o telefone |Faz uma chamada de voz automatizada para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. Esse número de telefone não está sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. O usuário é solicitado a responder ao texto, a mensagem com o código de verificação, ou a inserir o código de verificação na interface de entrada. |
| Notificação pelo aplicativo móvel |Envia uma notificação por push para o telefone ou o dispositivo registrado. O usuário vê a notificação e seleciona **Verificar** para concluir a verificação. <br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação de aplicativo móvel |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de acesso.<br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Como habilitar/desabilitar métodos de autenticação
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até a página de configurações do serviço MFA de acordo com as instruções fornecidas no início deste artigo.
3. Na página Configurações do Serviço, em Opções de verificação, marque/desmarque as opções que deseja usar.
   ![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Clique em **Salvar**.
5. Clique em **fechar**
