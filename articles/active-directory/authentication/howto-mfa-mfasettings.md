---
title: Configurar a autenticação multifator do Azure-Azure Active Directory
description: Este artigo descreve como definir as configurações de Autenticação Multifator do Azure no portal do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6260a619ad3dfda65fcdfc1180cba4002dd23d0
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499891"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar a Autenticação Multifator do Azure

Este artigo ajuda a gerenciar as configurações de Autenticação Multifator do Azure no portal do Azure. Ele aborda diversos tópicos que ajudarão você a aproveitar ao máximo a Autenticação Multifator do Azure. Nem todos os recursos estão disponíveis em todas as versões da autenticação multifator do Azure.

Você pode acessar as configurações relacionadas à autenticação multifator do Azure no portal do Azure navegando até **Azure Active Directory** > **MFA**.

![Portal do Azure - Definir as configurações de Autenticação Multifator do Microsoft Azure Active Directory](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Configurações

Algumas dessas configurações se aplicam ao servidor MFA, Azure MFA ou ambos.

| Recurso | Descrição |
| ------- | ----------- |
| Bloqueio de conta | Bloqueie contas temporariamente no serviço de autenticação multifator se houver muitas tentativas de autenticação negadas seguidas. Este recurso se aplica somente a usuários que inserem um PIN para autenticar. (Servidor MFA) |
| [Bloquear/desbloquear usuários](#block-and-unblock-users) | Usado para impedir que usuários específicos possam receber solicitações de autenticação multifator. Qualquer tentativa de autenticação de usuários bloqueados é negada automaticamente. Os usuários permanecem bloqueados por 90 dias a contar do momento em que são bloqueados. |
| [Alerta de fraude](#fraud-alert) | Definir configurações relacionadas à capacidade dos usuários de relatar solicitações de verificação fraudulentas |
| Notificações | Habilite notificações de eventos do servidor MFA. |
| [Tokens OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Usado em ambientes de MFA do Azure baseados em nuvem para gerenciar tokens OATH para os usuários. |
| [Configurações de chamada telefônica](#phone-call-settings) | Defina as configurações relacionadas para chamadas telefônicas e saudações para ambientes de nuvem e locais. |
| Provedores | Isso mostrará quaisquer provedores de autenticação existentes que você possa ter associado a sua conta. Novos provedores de autenticação não podem ser criados a partir de 1 de setembro de 2018 |

## <a name="manage-mfa-server"></a>Gerenciar Servidor de MFA

As configurações nesta seção são apenas para o servidor MFA.

| Recurso | Descrição |
| ------- | ----------- |
| Configurações do servidor | Faça o download do Servidor MFA e gere credenciais de ativação para inicializar seu ambiente |
| [Desvio único](#one-time-bypass) | Permita que um usuário se autentique sem executar a verificação em duas etapas por um período limitado. |
| [Regras de cache](#caching-rules) |  O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações ocorram automaticamente depois que o usuário conclui a primeira verificação em andamento. |
| Status do servidor | Veja o status dos seus servidores MFA locais incluindo versão, status, IP e última hora e data de comunicação. |

## <a name="activity-report"></a>Relatório de atividades

O relatório disponível aqui é específico para o servidor de MFA (local). Para os relatório do Azure MFA (nuvem) veja o relatório de entradas no Microsoft Azure Active Directory.

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear usuários

Use o recurso _bloquear e desbloquear usuários_ para impedir que os usuários recebam solicitações de autenticação. Qualquer tentativa de autenticação de usuários bloqueados é negada automaticamente. Os usuários permanecem bloqueados por 90 dias a contar do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um usuário

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **MFA** > **Bloquear/desbloquear usuários**.
3. Selecione **Adicionar** para bloquear um usuário.
4. Selecione o **Grupo de Replicação**. Insira o nome de usuário para a usuária bloqueada como **username\@Domain.com**. Insira um comentário no campo **Motivo**.
5. Selecione **Adicionar** para concluir o bloqueio do usuário.

### <a name="unblock-a-user"></a>Desbloquear um usuário

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **MFA** > **Bloquear/desbloquear usuários**.
3. Selecione **Desbloquear** na coluna **Ação** ao lado do usuário a ser desbloqueado.
4. Insira um comentário no campo **Motivo do desbloqueio**.
5. Selecione **Desbloquear** para concluir o desbloqueio do usuário.

## <a name="fraud-alert"></a>Alertas de Fraude

Configure o recurso de _alerta de fraude_ para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. Os usuários podem relatar tentativas de fraude usando o aplicativo móvel ou pelo telefone.

### <a name="turn-on-fraud-alerts"></a>Ativar alertas de fraude

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **MFA** > **Alerta de fraude**.
3. Defina a configuração **Permitir que os usuários enviem alertas de fraude** como **Ativada**.
4. Clique em **Salvar**.

### <a name="configuration-options"></a>Opções de configuração

* **Bloquear usuário quando fraude for relatada**: se um usuário relatar uma fraude, a respectiva conta será bloqueada por 90 dias ou até que um administrador a desbloqueie. Um administrador pode examinar as entradas usando o relatório de entrada e tomar as devidas ações para evitar futuras fraudes. Um administrador pode, então, [desbloquear](#unblock-a-user) a conta do usuário.
* **Código para relatar fraude durante a saudação inicial**: quando os usuários recebem um telefonema para realizar a verificação em duas etapas, eles normalmente pressionam **#** para confirmar sua conexão. Para relatar fraude, o usuário insere um código antes de pressionar **#** . Esse código é **0** por padrão, mas você pode personalizá-lo.

   >[!NOTE]
   >As saudações de voz padrão da Microsoft instruem os usuários a pressionar **0#** para enviar um alerta de fraude. Se você quiser usar um código diferente de **0**, grave e carregue suas próprias saudações de voz personalizadas com instruções aos usuários.
   >

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **Entradas**. O relatório de fraudes agora faz parte do relatório de entradas padrão do Azure AD.

## <a name="phone-call-settings"></a>Configurações de ligação telefônica

### <a name="caller-id"></a>ID do chamador

**Número de identificação do chamador MFA** -isso é o número que os usuários verão em seus telefones. São permitidos apenas números baseado nos EUA.

>[!NOTE]
>Quando as chamadas da Autenticação Multifator são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Sendo assim, a ID de chamadas não é garantida, mesmo que o sistema da Autenticação Multifator sempre a envie.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Você pode usar suas próprias gravações ou saudações para a verificação em duas etapas com o recurso _mensagens de voz personalizadas_. Essas mensagens podem ser usadas com as gravações da Microsoft ou para substituí-las.

Antes de começar, esteja ciente das seguintes restrições:

* Os formatos de arquivo compatíveis são .wav e .mp3.
* O limite de tamanho de arquivo é de 5 MB.
* As mensagens de autenticação devem ter menos de 20 segundos. As mensagens que têm mais de 20 segundos podem causar falha na verificação. O usuário poderá não responder antes da conclusão da mensagem e a verificação atingirá o tempo limite.

### <a name="custom-message-language-behavior"></a>Comportamento de idioma de mensagem personalizada

Quando uma mensagem de voz personalizada é reproduzida para o usuário, o idioma da mensagem depende destes fatores:

* O idioma do usuário atual.
  * O idioma detectado pelo navegador do usuário.
  * Outros cenários de autenticação podem se comportar de maneira diferente.
* O idioma de quaisquer mensagens personalizadas disponíveis.
  * Esse idioma é escolhido pelo administrador quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com um idioma alemão:

* Um usuário que se autentica em alemão ouvirá a mensagem personalizada em alemão.
* Um usuário que se autentica em inglês ouvirá a mensagem em inglês padrão.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Navegue até **Azure Active Directory** > **MFA** > **Configurações de ligação telefônica**.
1. Selecione **Adicionar saudação**.
1. Escolha o tipo de saudação.
1. Escolha o idioma.
1. Selecione um arquivo de som .mp3 ou .wav para carregar.
1. Selecione **Adicionar**.

### <a name="custom-voice-message-defaults"></a>Padrões de mensagem de voz personalizada

Scripts de exemplo para a criação de mensagens personalizadas.

| Nome da mensagem | script |
| --- | --- |
| Autenticação bem-sucedida | Sua entrada foi verificada com êxito. Logo. |
| Prompt de ramal | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para continuar. |
| Confirmação de Fraude | Um alerta de fraude foi enviado. Para desbloquear sua conta, entre em contato com o suporte técnico de ti da sua empresa. |
| Saudação de fraude (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione a tralha zero para enviar um alerta de fraude. Isso notificará a equipe de ti da sua empresa e poderá bloquear outras tentativas de verificação. |
| A fraude relatou que um alerta de fraude foi enviado. | Para desbloquear sua conta, entre em contato com o suporte técnico de ti da sua empresa. |
| Ativação | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. |
| Repetição de autenticação negada | Verificação negada. |
| Repetir (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. |
| Saudação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. |
| Saudação (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Saudação de fraude (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft.  Insira seu PIN seguido pela tecla tralha para concluir a verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione a tralha zero para enviar um alerta de fraude. Isso notificará a equipe de ti da sua empresa e poderá bloquear outras tentativas de verificação. |
| Tentar novamente (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Prompt de ramal após os dígitos | Se já estiver nesse ramal, pressione a tecla tralha para continuar. |
| Autenticação negada | Desculpe, não é possível conectá-lo no momento. Tente novamente mais tarde. |
| Saudação de ativação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. |
| Repetição de ativação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir sua verificação. |
| Saudação de ativação (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Prompt de ramal antes dos dígitos | Obrigado por usar o sistema de verificação de entrada da Microsoft. Transfira esta chamada para a extensão... |

## <a name="one-time-bypass"></a>Bypass avulso

O recurso _bypass avulso_ permite que um usuário se autentique uma única vez sem executar a verificação em duas etapas. O bypass é temporário e expira após um número de segundos especificado. Quando o aplicativo móvel ou o telefone não estiver recebendo notificações ou chamadas telefônicas, você poderá permitir um bypass avulso para que o usuário possa acessar o recurso desejado.

### <a name="create-a-one-time-bypass"></a>Criar um bypass avulso

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **MFA** > **bypass avulso**.
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para o bypass.
5. Insira o nome de usuário como **\@nome de usuário Domain.com**. Insira o número de segundos que o bypass deve durar. Insira o motivo do bypass.
6. Selecione **Adicionar**. O tempo limite entra em vigor imediatamente. O usuário precisa entrar antes que o bypass avulso se expire.

### <a name="view-the-one-time-bypass-report"></a>Para exibir o relatório de bypass avulso

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até **Azure Active Directory** > **MFA** > **bypass avulso**.

## <a name="caching-rules"></a>Regras de cache

Você pode definir um período de tempo para permitir tentativas de autenticação depois que um usuário é autenticado usando o recurso _cache_. As tentativas de autenticação subsequentes do usuário, dentro do período de tempo especificado, ocorrerão automaticamente. O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações ocorram automaticamente depois que o usuário conclui a primeira verificação em andamento.

>[!NOTE]
>O recurso de cache não se destina a ser usado para entradas no Azure AD (Azure Active Directory).

### <a name="set-up-caching"></a>Configurar o cache

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **MFA** > **regras de cache**.
3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** na lista suspensa. Insira o número máximo de **segundos de cache**.
5. Se necessário, selecione um tipo de autenticação e especifique um aplicativo.
6. Selecione **Adicionar**.

## <a name="mfa-service-settings"></a>Configurações de serviço MFA

As configurações para senhas de aplicativo, IPs, opções de verificação, confiáveis e lembrar da autenticação multifator para Autenticação Multifator do Azure pode ser encontrada nas configurações de serviço. Configurações de serviço podem ser acessadas no portal do Azure, navegando até **Azure Active Directory** > **MFA** > **Introdução**  >  **Configurar** > **Configurações adicionais de MFA baseado em nuvem**.

![Configurações de Serviço de Autenticação Multifator do Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Senhas do aplicativo

Alguns aplicativos, como o Office 2010 ou anterior e o Apple Mail antes do iOS 11, não são compatíveis com a verificação em duas etapas. Os aplicativos não estão configurados para aceitar uma segunda verificação. Para usar esses aplicativos, use o recurso _senhas de aplicativo_. Você pode usar uma senha de aplicativo em vez da sua senha tradicional para permitir que um aplicativo ignore a verificação em duas etapas e continue funcionando.

Autenticação moderna fornece suporte ao clientes do Microsoft Office 2013 e posteriores. Os clientes do Office 2013, incluindo o Outlook, dão suporte a protocolos de autenticação moderna e podem ser habilitados para trabalhar com verificação em duas etapas. Assim que o cliente estiver habilitado, as senhas de aplicativo não serão necessárias para o cliente.

>[!NOTE]
>As senhas de aplicativo não funcionam com o acesso condicional com base nas políticas de autenticação multifator e na autenticação moderna.

### <a name="considerations-about-app-passwords"></a>Considerações sobre senhas de aplicativo

Ao usar senhas de aplicativo, considere os seguintes pontos importantes:

* As senhas de aplicativo são inseridas apenas uma vez por aplicativo. Os usuários não precisam manter o controle das senhas nem as inserir toda vez.
* A senha real é gerada automaticamente e não é fornecida pelo usuário. A senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Há um limite de 40 senhas por usuário.
* Os aplicativos que armazenam as senhas em cache e as usam em cenários locais podem começar a falhar porque a senha de aplicativo não é conhecida fora da conta corporativa ou de estudante. Um exemplo dessa situação é o dos emails do Exchange que estão locais, mas os emails arquivado que estão na nuvem. Nessa situação, a mesma senha não funciona.
* Depois que a Autenticação Multifator é habilitada na conta de um usuário, as senhas de aplicativo podem ser usadas com a maioria dos clientes não usados em navegador, como Outlook e Microsoft Skype for Business. Ações administrativas não podem ser executadas com o uso de senhas de aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell. As ações não podem ser executadas, mesmo que o usuário tenha uma conta administrativa. Para executar scripts do PowerShell, crie uma conta de serviço com uma senha forte e não habilite a conta para a verificação em duas etapas.

>[!WARNING]
>As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. As senhas de domínio são necessárias para autenticar localmente. As senhas de aplicativo são necessárias para autenticar na nuvem.

### <a name="guidance-for-app-password-names"></a>Diretrizes para nomes de senha de aplicativo

Os nomes das senhas de aplicativo devem refletir o dispositivo em que elas serão usadas. Se você tem um laptop que tem aplicativos não usados em navegador, como o Outlook, o Word e o Excel, crie uma senha de aplicativo chamada **Laptop** para esses aplicativos. Crie outra senha de aplicativo chamada **Desktop** para os mesmos aplicativos que são executados em um computador desktop.

>[!NOTE]
>É recomendável que você crie uma senha de aplicativo por dispositivo, em vez de senha de aplicativo por aplicativo.

### <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicativo federado ou de logon único

O Azure AD é compatível com federação ou SSO (logon único), com AD DS (Active Directory Domain Services) local do Windows Server. Se sua organização for federada com o Azure AD e você estiver usando a Autenticação Multifator do Azure, considere os seguintes pontos sobre as senhas de aplicativo.

>[!NOTE]
>Os pontos a seguir se aplicam somente a clientes federados (SSO).

* As senhas de aplicativo são verificadas pelo Azure AD e, portanto, ignoram a federação. A federação é usada ativamente apenas na configuração das senhas de aplicativo.
* O IdP (Provedor de Identidade) não é contatado para usuários federados (SSO), diferentemente do fluxo passivo. As senhas de aplicativo são armazenadas na conta corporativa ou de estudante. Se um usuário sai da empresa, as informações do usuário fluem para a conta corporativa ou de estudante usando o **DirSync** em tempo real. A desabilitação/exclusão da conta de pode levar até três horas para ser sincronizada, o que pode ocasionar atraso ao desabilitar/excluir a senha de aplicativo no Azure AD.
* As configurações do Controle de Acesso de cliente local não são respeitadas pelo recurso senhas de aplicativo.
* Nenhuma funcionalidade de registro em log/auditoria de autenticação local está disponível para ser usada com o recurso senhas de aplicativo.
* Algumas arquiteturas avançadas exigem uma combinação de credenciais para a verificação em duas etapas com clientes. Essas credenciais podem incluir um nome de usuário e senhas de conta corporativa ou de estudante, além das senhas de aplicativo. Os requisitos dependem de como a autenticação é realizada. Para clientes que se autenticam em uma infraestrutura local, um nome de usuário e uma senha de conta corporativa ou de estudante são necessários. Para clientes que se autenticam no Azure AD, é necessária uma senha de aplicativo.

  Por exemplo, suponha que você tem a seguinte arquitetura:

  * Sua instância local do Active Directory é federada com o Azure AD.
  * Você está usando o Exchange online.
  * Você está usando o Skype for Business localmente.
  * Você está usando a Autenticação Multifator do Azure.

  Nesse cenário, você usa as seguintes credenciais:

  * Para entrar no Skype for Business, use seu nome de usuário e senha da conta corporativa ou de estudante.
  * Para acessar o catálogo de endereços de um cliente Outlook que se conecta com o Exchange online, use uma senha de aplicativo.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os usuários criem senhas de aplicativo

Por padrão, os usuários não podem criar senhas de aplicativo. O recurso de senhas de aplicativo deve ser habilitado. Para permitir que os usuários criem senhas de aplicativo, faça o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, selecione a opção **Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores**.

### <a name="create-app-passwords"></a>Criar senhas de aplicativo

Os usuários podem criar senhas de aplicativo durante o registro inicial. O usuário tem a opção de criar senhas de aplicativo no final do processo de registro.

Os usuários também podem criar senhas de aplicativo após o registro. Para obter mais informações e etapas detalhadas para os usuários, consulte [O que são senhas de aplicativo na Autenticação Multifator do Azure?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>IPs confiáveis

O recurso _IPs confiáveis_ da Autenticação Multifator do Azure é usado por administradores de um locatário gerenciado ou federado. O recurso ignora a verificação em duas etapas para os usuários que se conectam pela intranet da empresa. O recurso está disponível na versão completa da Autenticação Multifator do Azure e não na versão gratuita para administradores. Para saber como obter a versão completa da Autenticação Multifator do Azure, consulte [Autenticação Multifator do Azure](multi-factor-authentication.md).

> [!NOTE]
> Os IPs confiáveis de MFA e o acesso condicional com locais nomeados só funcionam com endereços IPV4.

Se sua organização implantar a extensão NPS para fornecer MFA a aplicativos locais, o endereço IP de origem sempre parecerá ser o servidor NPS que passa pela tentativa de autenticação.

| Tipo de locatário do Azure AD | Opções do recurso IPs Confiáveis |
|:--- |:--- |
| Gerenciado |**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que tem permissão para ignorar a verificação em duas etapas de usuários que se conectam pela intranet da empresa.|
| Federado |**Todos os usuários federados**: todos os usuários federados que se conectam de dentro da organização tem permissão para ignorar a verificação em duas etapas. Os usuários ignoram a verificação usando uma declaração que é emitida pelos Serviços de Federação do Active Directory (AD FS).<br/>**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que tem permissão para ignorar a verificação em duas etapas de usuários que se conectam pela intranet da empresa. |

O bypass dos IPs Confiáveis funciona somente dentro da intranet da empresa. Se você selecionar a opção **Todos os usuários federados** e um usuário se conectar de fora da intranet da empresa, ele deverá se autenticar usando a verificação em duas etapas. O processo será o mesmo, ainda que o usuário apresente uma declaração do AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência do usuário final dentro da rede corporativa

Quando o recurso IPs Confiáveis é desabilitado, a verificação em duas etapas é obrigatória para fluxos do navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

Quando o recurso IPs Confiáveis é habilitado, a verificação em duas etapas *não* é obrigatória para fluxos do navegador. As senhas de aplicativo *não* são necessárias para aplicativos cliente avançados mais antigos, desde que o usuário não tenha criado uma senha de aplicativo. Uma vez que a senha de aplicativo esteja em uso, ela permanecerá necessária. 

### <a name="end-user-experience-outside-corpnet"></a>Experiência do usuário final fora da rede corporativa

Independentemente se o recurso IPs Confiáveis estiver habilitado, a verificação em duas etapas será necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Habilitar locais nomeados usando o acesso condicional

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **locais nomeados**de**acesso** > condicional.
3. Selecione **Novo local**.
4. Insira um nome para o local.
5. Selecione **Marcar como local confiável**.
6. Insira o intervalo de IP em notação CIDR, como **192.168.1.1/24**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Habilitar o recurso IPs confiáveis usando o acesso condicional

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **locais nomeados**de**acesso** > condicional.
3. Selecione **Configurar IPs confiáveis de MFA**.
4. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir:

   * **Para solicitações de usuários federados originárias da minha intranet**: Para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectarem pela rede corporativa ignorarão a verificação em duas etapas usando uma declaração que é emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo específico de IPs públicos**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, use a notação como **xxx.xxx.xxx.xxx/32**.
      * Você pode inserir até 50 intervalos de endereço IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.

5. Clique em **Salvar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Habilitar o recurso IPs Confiáveis, usando as configurações do serviço

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na esquerda, selecione **Azure Active Directory** > **Usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir (ou ambas):

   * **Para solicitações de usuários federados na minha intranet**: Para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectarem pela rede corporativa ignorarão a verificação em duas etapas usando uma declaração que é emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo específico de sub-redes de endereços IP**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, use a notação como **xxx.xxx.xxx.xxx/32**.
      * Você pode inserir até 50 intervalos de endereço IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.

6. Clique em **Salvar**.

## <a name="verification-methods"></a>Métodos de verificação

Você pode escolher os métodos de verificação que estarão disponíveis para os usuários. A tabela a seguir fornece uma visão geral dos métodos.

Quando os usuários registram suas contas na Autenticação Multifator do Azure, eles escolhem o método de verificação preferido nas opções que você habilitou. As diretrizes para o processo de registro são fornecidas em [Configurar minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md).

| Método | Descrição |
|:--- |:--- |
| Ligar para o telefone |Faz uma chamada de voz automatizada para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. O número de telefone não é sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. É solicitado que o usuário digite o código de verificação na interface de acesso. Esse processo é chamado de SMS unidirecional. SMS bidirecional significa que o usuário deve retornar um determinado código por SMS. O SMS bidirecional foi preterido e não terá compatibilidade depois de 14 de novembro de 2018. Os usuários configurados para o SMS bidirecional serão mudados automaticamente para verificação _Ligar para o telefone_ nessa oportunidade.|
| Notificação pelo aplicativo móvel |Envia uma notificação por push para o telefone ou o dispositivo registrado. O usuário vê a notificação e seleciona **Verificar** para concluir a verificação. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| O código de verificação do aplicativo móvel ou token de hardware |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Habilitar e desabilitar métodos de verificação

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, em **Opções de verificação**, selecione/cancele a seleção dos métodos a serem fornecidos aos usuários.
6. Clique em **Salvar**.

Detalhes adicionais sobre o uso de métodos de autenticação podem ser encontrados no artigo [O que são os métodos de autenticação](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Lembrar a Autenticação Multifator

O recurso _Lembrar a Autenticação Multifator_ para dispositivos e navegadores que são confiáveis para o usuário é um recurso gratuito para todos os usuários da Autenticação Multifator. Os usuários podem ignorar as verificações subsequentes durante um número especificado de dias, depois de se conectarem com êxito a um dispositivo usando a Autenticação Multifator. O recurso melhora a usabilidade, minimizando o número de vezes que um usuário tem que realizar a verificação em duas etapas no mesmo dispositivo.

>[!IMPORTANT]
>Se um dispositivo ou uma conta for comprometida, a lembrança da Autenticação Multifator para dispositivos confiáveis poderá afetar a segurança. Caso uma conta corporativa seja comprometida ou um dispositivo confiável seja perdido ou roubado, você deve [restaurar a Autenticação Multifator em todos os dispositivos](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A ação de restaurar revoga o status de confiável de todos os dispositivos, e o usuário precisará executar a verificação em duas etapas novamente. Você também pode instruir seus usuários a restaurar a Autenticação Multifator em seus próprios dispositivos com as instruções disponíveis em [Gerenciar as configurações da verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Como o recurso funciona

O recurso Lembrar a Autenticação Multifator define um cookie persistente no navegador quando um usuário seleciona a opção **Não perguntar novamente durante X dias** ao entrar. O usuário não será solicitado novamente a realizar a Autenticação Multifator nesse mesmo navegador até que o cookie se expire. Caso o usuário abra um navegador diferente no mesmo dispositivo ou limpe os cookies, ele será solicitado a verificar novamente.

A opção **Não perguntar novamente durante X dias** não é mostrada em aplicativos sem navegador, independentemente se o aplicativo é compatível com a autenticação moderna. Esses aplicativos usam _tokens de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica se a última verificação em duas etapas ocorreu dentro do número especificado de dias.

O recurso reduz o número de autenticações em aplicativos Web, que normalmente solicitam todas as vezes. O recurso aumenta o número de autenticações de clientes de autenticação moderna, que normalmente solicitam a cada 90 dias. Também pode aumentar o número de autenticações quando combinadas com políticas de acesso condicional.

>[!IMPORTANT]
>O recurso **Lembrar a Autenticação Multifator** não é compatível com o recurso **Mantenha-me conectado** do AD FS, em que os usuários realizam a verificação em duas etapas no AD FS por meio do Servidor de Autenticação Multifator do Azure ou de uma solução de autenticação multifator de terceiros.
>
>Se os usuários selecionarem **Mantenha-me conectado** no AD FS e também marcarem seus dispositivos como confiáveis para a Autenticação Multifator, eles não serão verificados automaticamente após o fim do número de dias da **Lembrar a Autenticação Multifator**. O Azure AD solicitará uma nova verificação em duas etapas, mas o AD FS retornará um token com a declaração e a data originais da Autenticação Multifator em vez de realizar a verificação em duas etapas novamente. **Essa reação dá início a um loop de verificação entre o Microsoft Azure Active Directory e o AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar a opção Lembrar a Autenticação Multifator

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, **Gerenciar Lembrar a Autenticação Multifator**, selecione a opção **Permite aos usuários lembrar a autenticação multifator em dispositivos que eles confiam**.
6. Defina o número de dias a permitir que os dispositivos confiáveis ignorem a verificação em duas etapas. O padrão é 14 dias.
7. Clique em **Salvar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar o recurso Lembrar a Autenticação Multifator, os usuários poderão marcar um dispositivo como confiável ao entrarem e selecionarem **Não perguntar novamente**.

## <a name="next-steps"></a>Próximas etapas

[Modificar a identidade visual da página de entrada do Azure AD](../fundamentals/customize-branding.md)
