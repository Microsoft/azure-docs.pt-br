---
title: Configurar a autenticação multifator do Azure AD-Azure Active Directory
description: Saiba como definir as configurações para a autenticação multifator do Azure AD no portal do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8f2bd316c733f4680a266d609e1cc95a4879016d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198516"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Definir as configurações de autenticação multifator do Azure AD

Para personalizar a experiência do usuário final para a autenticação multifator do Azure AD, você pode configurar opções para configurações como os limites de bloqueio de conta ou notificações e alertas de fraude. Algumas configurações estão diretamente no portal do Azure para Azure Active Directory (AD do Azure) e algumas em um portal de autenticação multifator do Azure AD separado.

As seguintes configurações de autenticação multifator do Azure AD estão disponíveis no portal do Azure:

| Recurso | Descrição |
| ------- | ----------- |
| [Bloqueio de conta](#account-lockout) | Bloqueie temporariamente as contas usando a autenticação multifator do Azure AD se houver muitas tentativas de autenticação negadas em uma linha. Este recurso se aplica somente a usuários que inserem um PIN para autenticar. (Servidor MFA) |
| [Bloquear/desbloquear usuários](#block-and-unblock-users) | Impedir que usuários específicos possam receber solicitações de autenticação multifator do Azure AD. Qualquer tentativa de autenticação de usuários bloqueados é negada automaticamente. Os usuários permanecem bloqueados por 90 dias a partir do momento em que são bloqueados ou desbloqueados manualmente. |
| [Alerta de fraude](#fraud-alert) | Defina as configurações que permitem que os usuários relatem solicitações de verificação fraudulentas. |
| [Notificações](#notifications) | Habilite notificações de eventos do servidor MFA. |
| [Tokens OATH](concept-authentication-oath-tokens.md) | Usado em ambientes de MFA do Azure AD baseados em nuvem para gerenciar tokens OATH para usuários. |
| [Configurações de chamada telefônica](#phone-call-settings) | Defina as configurações relacionadas para chamadas telefônicas e saudações para ambientes de nuvem e locais. |
| Provedores | Isso mostrará quaisquer provedores de autenticação existentes que você possa ter associado a sua conta. Novos provedores de autenticação não podem ser criados a partir de 1 de setembro de 2018 |

![Portal do Azure - Definir as configurações de Autenticação Multifator do Microsoft Azure Active Directory](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Bloqueio de conta

Para evitar tentativas de MFA repetidas como parte de um ataque, as configurações de bloqueio de conta permitem especificar quantas tentativas de falha permitir antes que a conta seja bloqueada por um período de tempo. As configurações de bloqueio de conta são aplicadas somente quando um código PIN é inserido para o prompt do MFA.

As seguintes configurações estão disponíveis:

* Número de negações de MFA para disparar o bloqueio de conta
* Minutos até o contador de bloqueio de conta ser redefinido
* Minutos até que a conta seja desbloqueada automaticamente

Para definir as configurações de bloqueio de conta, conclua as seguintes configurações:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Navegue até **Azure Active Directory**  >    >    >  **bloqueio de conta** do MFA de segurança.
1. Insira os valores necessários para o seu ambiente e, em seguida, selecione **salvar**.

    ![Captura de tela das configurações de bloqueio de conta no portal do Azure](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear usuários

Se o dispositivo de um usuário tiver sido perdido ou roubado, você poderá bloquear as tentativas de autenticação multifator do Azure AD para a conta associada. Qualquer tentativa de autenticação multifator do Azure AD para usuários bloqueados é negada automaticamente. Os usuários permanecem bloqueados por 90 dias a contar do momento em que são bloqueados. Publicamos um vídeo sobre [como bloquear e desbloquear usuários em seu locatário](https://www.youtube.com/watch?v=WdeE1On4S1o) para mostrar como fazer isso.

### <a name="block-a-user"></a>Bloquear um usuário

Para bloquear um usuário, conclua as seguintes etapas:

1. Navegue até **Azure Active Directory** > **Segurança** > **MFA** > **Bloquear/desbloquear usuários**.
1. Selecione **Adicionar** para bloquear um usuário.
1. Insira o nome de usuário como `username@domain.com` , em seguida, forneça um comentário no campo *motivo* .
1. Quando estiver pronto, selecione **OK** para bloquear o usuário.

### <a name="unblock-a-user"></a>Desbloquear um usuário

Para desbloquear um usuário, conclua as seguintes etapas:

1. Navegue até **Azure Active Directory** > **Segurança** > **MFA** > **Bloquear/desbloquear usuários**.
1. Na coluna *ação* ao lado do usuário desejado, selecione **desbloquear**.
1. Insira um comentário no campo *Motivo do desbloqueio*.
1. Quando estiver pronto, selecione **OK** para desbloquear o usuário.

## <a name="fraud-alert"></a>Alerta de fraude

O recurso de alerta de fraude permite que os usuários relatem tentativas fraudulentas de acessar seus recursos. Quando um prompt de MFA desconhecido e suspeito é recebido, os usuários podem relatar a tentativa de fraude usando o aplicativo Microsoft Authenticator ou por meio de seu telefone.

As seguintes opções de configuração de alerta de fraude estão disponíveis:

* **Bloquear automaticamente os usuários que relatam fraude**: se um usuário relatar fraude, as tentativas de autenticação do Azure ad MFA para a conta de usuário serão bloqueadas por 90 dias ou até que um administrador desbloqueie sua conta. Um administrador pode examinar as entradas usando o relatório de entrada e tomar as devidas ações para evitar futuras fraudes. Um administrador pode, então, [desbloquear](#unblock-a-user) a conta do usuário.
* **Código para relatar fraude durante a saudação inicial**: quando os usuários recebem uma chamada telefônica para executar a autenticação multifator, eles normalmente pressionam **#** para confirmar sua entrada. Para relatar fraude, o usuário insere um código antes de pressionar **#** . Esse código é **0** por padrão, mas você pode personalizá-lo.

   > [!NOTE]
   > As saudações de voz padrão da Microsoft instruem os usuários a pressionar **0#** para enviar um alerta de fraude. Se você quiser usar um código diferente de **0**, grave e carregue suas próprias saudações de voz personalizadas com instruções aos usuários.

Para habilitar e configurar alertas de fraude, conclua as seguintes etapas:

1. Navegue para **Azure Active Directory** > **Segurança** > **MFA** > **Alerta de fraude**.
1. Defina a configuração *Permitir que os usuários enviem alertas de fraude* como **Ativada**.
1. Configure o *bloqueio automático de usuários que relatam fraude* ou *código para relatar fraude durante a configuração de saudação inicial* , conforme desejado.
1. Quando estiver pronto, selecione **Salvar**.

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude

Selecione **Azure Active Directory** > **Credenciais** > **Detalhes da autenticação**. O relatório de fraudes agora faz parte do relatório padrão de entradas do Azure Active Directory e aparecerá nos **“Detalhes do resultado”** como MFA negado, código de fraude inserido.
 
## <a name="notifications"></a>Notificações

As notificações por email podem ser configuradas quando os usuários relatam alertas de fraude. Essas notificações são normalmente enviadas aos administradores de identidade, pois as credenciais da conta do usuário provavelmente são comprometidas. O exemplo a seguir mostra a aparência de um email de notificação de alerta de fraude:

![Email de notificação de alerta de fraude de exemplo](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Para configurar notificações de alerta de fraude, conclua as seguintes configurações:

1. Navegue até **Azure Active Directory**  >    >  notificações de **autenticação multifator** de segurança  >  .
1. Insira o endereço de email a ser adicionado à próxima caixa.
1. Para remover um endereço de email existente, selecione a opção **...** ao lado do endereço de email desejado e, em seguida, selecione **excluir**.
1. Quando estiver pronto, selecione **Salvar**.

## <a name="oath-tokens"></a>Tokens OATH

O Azure AD dá suporte ao uso de tokens OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem adquirir esses tokens do fornecedor que preferirem.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta ou semente previamente programada no token. Essas chaves devem ser inseridas no Azure AD, conforme descrito nas etapas a seguir. As chaves secretas estão limitadas a 128 caracteres, que podem não ser compatíveis com todos os tokens. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e dígitos *1-7* e deve ser codificada em *Base32*.

Os tokens de hardware OATH TOTP programáveis que podem ser propagados também podem ser configurados com o Azure AD no fluxo de instalação do token de software.

![Carregar tokens OATH para a folha de tokens OATH da MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Depois que os tokens são adquiridos, eles precisam ser carregados em um arquivo com formato CSV (valores separados por vírgulas) que inclua UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo, como mostrado no seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Verifique se você incluiu a linha de cabeçalho no arquivo CSV.

Após a formatação correta com um arquivo CSV, um administrador pode entrar no portal do Azure, navegar para **Azure Active Directory > Segurança > MFA > Tokens OATH** e carregar o arquivo CSV resultante.

Dependendo do tamanho do arquivo CSV, ele poderá levar alguns minutos para ser processado. Selecione o botão **Atualizar** para obter o status atual. Se houver erros no arquivo, você poderá baixar um arquivo CSV que lista os erros para que você possa resolvê-los. Os nomes de campo no arquivo CSV baixado são diferentes da versão carregada.

Depois que os erros forem resolvidos, o administrador poderá ativar cada chave selecionando **Ativar** para o token e inserindo a OTP exibido no token.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos de autenticação, como o Microsoft Authenticator, configurados para uso a qualquer momento.

## <a name="phone-call-settings"></a>Configurações de chamada telefônica

Se os usuários receberem chamadas telefônicas para solicitações do MFA, você poderá configurar sua experiência, como ID do chamador ou saudação de voz que eles ouvem.

Na Estados Unidos, se você ainda não configurou a ID do chamador MFA, as chamadas de voz da Microsoft vêm dos números a seguir. Se estiver usando filtros de spam, certifique-se de excluir estes números:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Quando as chamadas da autenticação multifator do Azure AD são colocadas por meio da rede de telefone pública, às vezes, as chamadas são roteadas por meio de uma operadora que não dá suporte à ID do chamador. Por isso, a ID do chamador não é garantida, embora a autenticação multifator do Azure AD sempre a envie. Isso se aplica a chamadas telefônicas e a mensagens de texto fornecidas pela autenticação multifator do Azure AD. Se você precisar validar que uma mensagem de texto é da autenticação multifator do Azure AD, consulte [quais códigos curtos do SMS são usados para enviar mensagens?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Para configurar seu próprio número de identificação de chamador, conclua as seguintes etapas:

1. Navegue para **Azure Active Directory** > **Segurança** > **MFA** > **Configurações de chamada telefônica**.
1. Defina o **número de identificação do chamador MFA** como o número que você deseja que os usuários vejam em seu telefone. São permitidos apenas números baseado nos EUA.
1. Quando estiver pronto, selecione **Salvar**.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Você pode usar suas próprias gravações ou saudações para a autenticação multifator do Azure AD com o recurso de mensagens de voz personalizadas. Essas mensagens podem ser usadas além de ou para substituir as gravações padrão da Microsoft.

Antes de começar, esteja ciente das seguintes restrições:

* Os formatos de arquivo com suporte são *. wav* e *. mp3*.
* O limite de tamanho de arquivo é de 1 MB.
* As mensagens de autenticação devem ter menos de 20 segundos. As mensagens que têm mais de 20 segundos podem causar falha na verificação. O usuário poderá não responder antes da conclusão da mensagem e a verificação atingirá o tempo limite.

### <a name="custom-message-language-behavior"></a>Comportamento de idioma de mensagem personalizada

Quando uma mensagem de voz personalizada é reproduzida para o usuário, o idioma da mensagem depende dos seguintes fatores:

* O idioma do usuário atual.
  * O idioma detectado pelo navegador do usuário.
  * Outros cenários de autenticação podem se comportar de maneira diferente.
* O idioma de quaisquer mensagens personalizadas disponíveis.
  * Esse idioma é escolhido pelo administrador quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com um idioma alemão:

* Um usuário que se autentica em alemão ouvirá a mensagem personalizada em alemão.
* Um usuário que se autentica em inglês ouvirá a mensagem em inglês padrão.

### <a name="custom-voice-message-defaults"></a>Padrões de mensagem de voz personalizada

Os scripts de exemplo a seguir podem ser usados para criar suas próprias mensagens personalizadas. Essas frases serão os padrões se você não configurar suas próprias mensagens personalizadas:

| Nome da mensagem | Script |
| --- | --- |
| Autenticação bem-sucedida | Sua entrada foi verificada com êxito. Até logo. |
| Prompt de ramal | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Pressione a tecla # para continuar. |
| Confirmação de fraude | Um alerta de fraude foi enviado. Para desbloquear sua conta, entre em contato com o suporte técnico de TI da sua empresa. |
| Saudação de fraude (Padrão) | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Pressione a tecla # para concluir sua verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione 0 # para enviar um alerta de fraude. Isso avisará a equipe de TI da sua empresa para bloquear novas tentativas de verificação. |
| Fraude relatada. Um alerta de fraude foi enviado. | Para desbloquear sua conta, entre em contato com o suporte técnico de TI da sua empresa. |
| Ativação | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla # para concluir sua verificação. |
| Repetição de autenticação negada | Verificação negada. |
| Repetir (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla # para concluir sua verificação. |
| Saudação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla # para concluir sua verificação. |
| Saudação (PIN) | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Insira seu PIN seguido pela tecla # para concluir a verificação. |
| Saudação de fraude (PIN) | Obrigado por usar o sistema de verificação de credenciais da Microsoft.  Insira seu PIN seguido pela tecla # para concluir a verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione 0 # para enviar um alerta de fraude. Isso avisará a equipe de TI da sua empresa para bloquear novas tentativas de verificação. |
| Tentar de novo (PIN) | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Insira seu PIN seguido pela tecla # para concluir a verificação. |
| Prompt de ramal após os dígitos | Se já estiver nesse ramal, pressione a tecla # para continuar. |
| Autenticação negada | Desculpe, não é possível fazer a conexão no momento. Tente novamente mais tarde. |
| Saudação de ativação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla # para concluir sua verificação. |
| Repetição de ativação (Padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla # para concluir sua verificação. |
| Saudação de ativação (PIN) | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Insira seu PIN seguido pela tecla # para concluir a verificação. |
| Prompt de ramal antes dos dígitos | Obrigado por usar o sistema de verificação de credenciais da Microsoft. Transfira esta chamada para o ramal… |

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

Para usar suas próprias mensagens personalizadas, conclua as seguintes etapas:

1. Navegue para **Azure Active Directory** > **Segurança** > **MFA** > **Configurações de chamada telefônica**.
1. Selecione **Adicionar saudação**.
1. Escolha o **tipo** de saudação, como *saudação (padrão)* ou  *autenticação bem-sucedida*.
1. Selecione o **idioma**, com base na seção anterior sobre o [comportamento da linguagem de mensagem personalizada](#custom-message-language-behavior).
1. Procure e selecione um arquivo de som *. mp3* ou *. wav* para carregar.
1. Quando estiver pronto, selecione **Adicionar** e **salvar**.

## <a name="mfa-service-settings"></a>Configurações de serviço MFA

As configurações para senhas de aplicativo, IPs confiáveis, opções de verificação e lembrar a autenticação multifator para a autenticação multifator do Azure AD podem ser encontradas nas configurações de serviço. Isso é mais de um portal herdado e não faz parte do portal do Azure AD normal.

Configurações de serviço podem ser acessadas no portal do Azure, navegando até **Azure Active Directory** > **Segurança** > **MFA** > **Introdução** > **Configurar** > **Configurações adicionais de MFA baseadas em nuvem**. Uma nova janela ou guia é aberta com opções de *configurações de serviço* adicionais.

## <a name="trusted-ips"></a>IPs confiáveis

O recurso _IPs confiáveis_ da autenticação multifator do Azure ad ignora os prompts de autenticação multifator para os usuários que entram em um intervalo de endereços IP definido. Você pode definir intervalos de IP confiáveis para seus ambientes locais para quando os usuários estiverem em um desses locais, não há nenhum prompt de autenticação multifator do Azure AD.

> [!NOTE]
> Os IPs confiáveis podem incluir intervalos de IP privados somente quando você usa o servidor MFA. Para a autenticação multifator do Azure AD baseada em nuvem, você só pode usar intervalos de endereços IP públicos.
>
> Os intervalos de IPv6 só têm suporte na interface [local nomeada (versão prévia)](../conditional-access/location-condition.md#preview-features) .

Se sua organização implantar a extensão NPS para fornecer MFA a aplicativos locais, o endereço IP de origem sempre parecerá ser o servidor NPS que passa pela tentativa de autenticação.

| Tipo de locatário do Azure AD | Opções de recurso de IP confiável |
|:--- |:--- |
| Gerenciados |**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que podem ignorar a autenticação multifator para usuários que entram na intranet da empresa. Podem ser configurados no máximo 50 intervalos de IP confiáveis.|
| Federado |**Todos os usuários federados**: todos os usuários federados que entram de dentro da organização podem ignorar a autenticação multifator. Os usuários ignoram a verificação usando uma declaração que é emitida pelos Serviços de Federação do Active Directory (AD FS).<br/>**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que podem ignorar a autenticação multifator para usuários que entram na intranet da empresa. |

O bypass de IP confiável funciona somente de dentro da intranet da empresa. Se você selecionar a opção **todos os usuários federados** e um usuário entrar de fora da intranet da empresa, o usuário precisará se autenticar usando a autenticação multifator. O processo será o mesmo, ainda que o usuário apresente uma declaração do AD FS.

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência do usuário final dentro da rede corporativa

Quando o recurso IPs confiáveis é desabilitado, a autenticação multifator é necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

Quando são usados IPs confiáveis, a autenticação multifator não é necessária para fluxos de navegador. As senhas de aplicativo não são necessárias para aplicativos cliente avançados mais antigos, desde que o usuário não tenha criado uma senha de aplicativo. Uma vez que a senha de aplicativo esteja em uso, ela permanecerá necessária.

### <a name="end-user-experience-outside-corpnet"></a>Experiência do usuário final fora da rede corporativa

Independentemente de o IP confiável ser definido, a autenticação multifator é necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Habilitar locais nomeados usando o acesso condicional

Você pode usar regras de acesso condicional para definir locais nomeados usando as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure Active Directory** e navegue até **segurança**  >  **acesso condicional**  >  **locais nomeados**.
1. Selecione **Novo local**.
1. Insira um nome para o local.
1. Selecione **Marcar como local confiável**.
1. Insira o intervalo de IP na notação CIDR para seu ambiente, como *40.77.182.32/27*.
1. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Habilitar o recurso IPs confiáveis usando o acesso condicional

Para habilitar IPs confiáveis usando políticas de acesso condicional, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure Active Directory** e navegue até **segurança**  >   **acesso condicional**  >  **locais nomeados**.
1. Selecione **Configurar IPs confiáveis de MFA**.
1. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir:

   * **Para solicitações de usuários federados originárias da minha intranet**: Para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectam da rede corporativa ignoram a autenticação multifator usando uma declaração emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo específico de IPs públicos**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, use a notação como **xxx.xxx.xxx.xxx/32**.
      * Você pode inserir até 50 intervalos de endereço IP. Os usuários que se conectam a partir desses endereços IP ignoram a autenticação multifator.

1. Clique em **Salvar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Habilitar o recurso IPs Confiáveis, usando as configurações do serviço

Se você não quiser usar políticas de acesso condicional para habilitar IPs confiáveis, poderá definir as *configurações de serviço* para a autenticação multifator do Azure ad usando as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure Active Directory** e, em seguida, escolha **usuários**.
1. Selecione **Autenticação Multifator**.
1. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
1. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir (ou ambas):

   * **Para solicitações de usuários federados na minha intranet**: Para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectam da rede corporativa ignoram a autenticação multifator usando uma declaração emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo específico de sub-redes de endereços IP**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, use a notação como **xxx.xxx.xxx.xxx/32**.
      * Você pode inserir até 50 intervalos de endereço IP. Os usuários que se conectam a partir desses endereços IP ignoram a autenticação multifator.

1. Clique em **Salvar**.

## <a name="verification-methods"></a>Métodos de verificação

Você pode escolher os métodos de verificação que estão disponíveis para seus usuários no portal de configurações de serviço. Quando os usuários registram suas contas para a autenticação multifator do Azure AD, eles escolhem o método de verificação preferencial nas opções que você habilitou. As diretrizes para o processo de registro de usuário são fornecidas em [configurar minha conta para autenticação multifator](../user-help/multi-factor-authentication-end-user-first-time.md).

Os seguintes métodos de verificação estão disponíveis:

| Método | Descrição |
|:--- |:--- |
| Ligue para o telefone |Faz uma chamada de voz automatizada para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. O número de telefone não é sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. É solicitado que o usuário digite o código de verificação na interface de acesso. Esse processo é chamado de SMS unidirecional. SMS bidirecional significa que o usuário deve retornar um determinado código por SMS. O SMS bidirecional foi preterido e não terá compatibilidade depois de 14 de novembro de 2018. Os administradores devem habilitar outro método para os usuários que já usaram o SMS bidirecional.|
| Notificação pelo aplicativo móvel |Envia uma notificação por push para o telefone ou o dispositivo registrado. O usuário vê a notificação e seleciona **Verificar** para concluir a verificação. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| O código de verificação do aplicativo móvel ou token de hardware |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Para obter mais informações, consulte [quais métodos de autenticação e verificação estão disponíveis no Azure ad?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Habilitar e desabilitar métodos de verificação

Para habilitar ou desabilitar os métodos de verificação, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure Active Directory** e, em seguida, escolha **usuários**.
1. Selecione **Autenticação Multifator**.
1. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
1. Na página **Configurações do Serviço**, em **Opções de verificação**, selecione/cancele a seleção dos métodos a serem fornecidos aos usuários.
1. Clique em **Save** (Salvar).

## <a name="remember-multi-factor-authentication"></a>Lembre-se da Autenticação Multifator

O recurso _lembrar a autenticação multifator_ permite que os usuários ignorem as verificações subsequentes por um número especificado de dias, depois de entrarem com êxito em um dispositivo usando a autenticação multifator. Para aumentar a usabilidade e minimizar o número de vezes que um usuário tem de executar a MFA no mesmo dispositivo, selecione uma duração de 90 dias ou mais.

> [!IMPORTANT]
> Se um dispositivo ou uma conta for comprometida, a lembrança da Autenticação Multifator para dispositivos confiáveis poderá afetar a segurança. Se uma conta corporativa for comprometida ou um dispositivo confiável for perdido, você deverá [Revogar sessões MFA](howto-mfa-userdevicesettings.md).
>
> A ação de restauração revoga o status confiável de todos os dispositivos e o usuário precisa executar a autenticação multifator novamente. Você também pode instruir seus usuários a restaurar a autenticação multifator em seus próprios dispositivos, conforme observado em [gerenciar suas configurações para autenticação multifator](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Como o recurso funciona

O recurso Lembrar a Autenticação Multifator define um cookie persistente no navegador quando um usuário seleciona a opção **Não perguntar novamente durante X dias** ao entrar. O usuário não será solicitado novamente a realizar a Autenticação Multifator nesse mesmo navegador até que o cookie se expire. Caso o usuário abra um navegador diferente no mesmo dispositivo ou limpe os cookies, ele será solicitado a verificar novamente.

A opção **Não perguntar novamente durante X dias** não é mostrada em aplicativos sem navegador, independentemente se o aplicativo é compatível com a autenticação moderna. Esses aplicativos usam _tokens de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica se a última autenticação multifator ocorreu dentro do número de dias especificado.

O recurso reduz o número de autenticações em aplicativos Web, que normalmente solicitam todas as vezes. O recurso pode aumentar o número de autenticações para clientes de autenticação modernos que normalmente solicitam a cada 90 dias, se uma duração menor for configurada. Também é possível aumentar o número de autenticações quando combinado com políticas de acesso condicional.

> [!IMPORTANT]
> O recurso **lembrar a autenticação multifator** não é compatível com o recurso **manter-me conectado** do AD FS, quando os usuários executam a autenticação multifator para AD FS por meio do Azure servidor de autenticação multifator ou uma solução de autenticação multifator de terceiros.
>
> Se os usuários selecionarem **Mantenha-me conectado** no AD FS e também marcarem seus dispositivos como confiáveis para a Autenticação Multifator, eles não serão verificados automaticamente após o fim do número de dias da **Lembrar a Autenticação Multifator**. O Azure AD solicita uma nova autenticação multifator, mas AD FS retorna um token com a declaração e a data originais da autenticação multifator, em vez de executar novamente a autenticação multifator. **Essa reação dá início a um loop de verificação entre o Microsoft Azure Active Directory e o AD FS.**
>
> O recurso **Lembrar Autenticação Multifator** não é compatível com usuários B2B e não será visível para eles ao entrarem nos locatários convidados.
>

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar a opção Lembrar a Autenticação Multifator

Para habilitar e configurar a opção para os usuários lembrarem seus status de MFA e ignorarem avisos, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure Active Directory** e, em seguida, escolha **usuários**.
1. Selecione **Autenticação Multifator**.
1. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
1. Na página **configurações de serviço** , em **lembrar autenticação multifator**, selecione a opção **permitir que os usuários se lembrem da autenticação multifator em dispositivos que confiam** .
1. Defina o número de dias para permitir que os dispositivos confiáveis ignorem a autenticação multifator. Para obter a melhor experiência do usuário, estenda a duração para *90* ou mais dias.
1. Clique em **Salvar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar o recurso lembrar a autenticação multifator, os usuários podem marcar um dispositivo como confiável ao entrarem selecionando a opção para **não perguntar novamente**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os métodos disponíveis para uso na autenticação multifator do Azure AD, consulte [quais métodos de autenticação e verificação estão disponíveis no Azure Active Directory?](concept-authentication-methods.md)
