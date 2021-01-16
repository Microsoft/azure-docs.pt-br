---
title: Perguntas e respostas sobre o aplicativo Microsoft Authenticator - Azure Active Directory
description: Perguntas frequentes sobre o aplicativo de autenticação e a verificação de dois fatores da Microsoft.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f799ed5c18537b862a4971075ae9107b7a722fdd
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251498"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Perguntas frequentes sobre o aplicativo Microsoft Authenticator

Este artigo responde a perguntas comuns sobre o aplicativo Microsoft Authenticator. Caso não encontre uma resposta para sua pergunta, vá para o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

O aplicativo Microsoft Authenticator substituiu o aplicativo Azure Authenticator e é o aplicativo recomendado quando você usa a autenticação multifator do Azure AD. O aplicativo Microsoft Authenticator está disponível para [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) e [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="registering-a-device"></a>Registrando um dispositivo

**P**: o registro de um dispositivo está concordando para dar acesso à empresa ou ao serviço ao meu dispositivo?

**R**: registrar um dispositivo dá ao seu dispositivo acesso aos serviços da sua organização e não permite que sua organização acesse seu dispositivo.

### <a name="too-many-app-permissions"></a>Muitas permissões de aplicativo

**P**: por que o aplicativo solicita tantas permissões?

**R**: aqui está a lista completa de permissões que podem ser solicitadas e como elas são usadas pelo aplicativo. As permissões específicas que você verá dependem do tipo de telefone que você tem. Às vezes, sua organização deseja saber seu **local** antes de permitir que você acesse determinados recursos. O aplicativo solicitará essa permissão somente se sua organização tiver uma política que exija o local.

### <a name="error-adding-account"></a>Erro ao adicionar conta

**P**: quando tento adicionar minha conta, recebo uma mensagem de erro dizendo "a conta que você está tentando adicionar não é válida no momento. Contate seu administrador para corrigir esse problema (validação de exclusividade). " O que devo fazer?

**R**: entre em contato com seu administrador e informe que você está impedido de adicionar sua conta ao autenticador devido a um problema de validação de exclusividade. Você precisará fornecer seu nome de usuário de entrada para que seu administrador possa procurar na sua organização.

### <a name="legacy-apns-support-deprecated"></a>Suporte a APNs herdado preterido

**P**: como a interface binária herdada para o serviço de notificação por push da Apple está sendo substituída em novembro de 2020, como posso continuar usando o fator de Microsoft Authenticator/telefone para entrar?

**R: a** [Apple anunciou a reprovação](https://developer.apple.com/news/?id=11042019a) de notificações por push que usam sua interface binária para dispositivos IOS, como os usados pelo fator de telefone. Para continuar a receber notificações por push, recomendamos que os usuários atualizem seu aplicativo autenticador para a versão mais recente do aplicativo. Enquanto isso, você pode contornar isso verificando manualmente as notificações no aplicativo autenticador.

### <a name="app-lock-feature"></a>Recurso de bloqueio de aplicativo

**P**: o que é o bloqueio de aplicativo e como posso usá-lo para ajudar a me manter mais seguro?

**R**: o bloqueio do aplicativo ajuda a manter os códigos de verificação única, as informações do aplicativo e as configurações do aplicativo mais seguros. Quando o bloqueio de aplicativo estiver habilitado, você será solicitado a autenticar usando o PIN do dispositivo ou biométrica toda vez que abrir o autenticador. O bloqueio de aplicativo também ajuda a garantir que você seja o único que pode aprovar notificações solicitando seu PIN ou biométrica sempre que você aprova uma notificação de entrada. Você pode ativar ou desativar o bloqueio do aplicativo na página Configurações do autenticador. Por padrão, o bloqueio de aplicativo é ativado quando você configura um PIN ou biométrica em seu dispositivo.<br><br>Infelizmente, não há nenhuma garantia de que o bloqueio do aplicativo irá impedir que alguém acesse o autenticador. Isso ocorre porque o registro do dispositivo pode ocorrer em outros locais fora do autenticador, como nas configurações da conta do Android ou no aplicativo Portal da Empresa.

### <a name="windows-mobile-retired"></a>Windows Mobile desativado

**P**: tenho um dispositivo Windows Mobile e o Microsoft Authenticator no Windows Mobile foi preterido. Posso continuar a autenticação usando o aplicativo?

**R**: todas as autenticações que usam o Microsoft Authenticator no Windows Mobile serão desativadas após 15 de julho de 2020. É altamente recomendável que você use um método de autenticação alternativo para evitar ser bloqueado de suas contas.<br>As opções alternativas para usuários empresariais incluem:<br><ul><li>Configurando o Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [Ios](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Configurando o SMS](multi-factor-authentication-setup-phone-number.md) para receber códigos de verificação.</li><li>Configurar o número de telefone para receber [chamadas telefônicas para verificar sua identidade](multi-factor-authentication-setup-office-phone.md).</li></ul><br>As opções alternativas para usuários pessoais conta Microsoft incluem:<br><ul><li>Configurando o Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [Ios](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Configurando um método de entrada alternativo (SMS ou email) atualizando suas informações de segurança na [página de segurança da conta da Microsoft](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Capturas de tela do Android

**P**: posso tirar capturas de tela de meus códigos de senha de uso único (OTP) no autenticador do Android?

**R: a** partir da versão 6.2003.1704 do autenticador Android, por padrão, todos os códigos de OTP ficam ocultos sempre que uma captura de tela do autenticador é tomada. Se você quiser ver seus códigos de OTP em capturas de tela ou permitir que outros aplicativos capturem o autenticador, você pode. Basta ativar a configuração de **captura de tela** no autenticador e reiniciar o aplicativo.

### <a name="delete-stored-data"></a>Excluir dados armazenados

**P**: quais dados o autenticador armazena em meu nome e como posso excluí-lo?

**R**: o aplicativo autenticador coleta três tipos de informações:<ul><li>Informações da conta que você fornece ao adicionar a conta. Esses dados podem ser removidos, removendo a conta.</li><li>Dados de log de diagnóstico que permanecem apenas no aplicativo até você selecionar **Enviar logs** no menu **Ajuda** do aplicativo para enviar logs à Microsoft. Esses logs podem conter dados pessoais, como endereços de email, endereços de servidor ou endereços IP. Eles também podem conter dados do dispositivo, como o nome do dispositivo e a versão do sistema operacional. Todos os dados pessoais coletados são limitados às informações necessárias para ajudar a solucionar problemas de aplicativos. Você pode procurar esses arquivos de log no aplicativo a qualquer momento para ver as informações que estão sendo coletadas. Se você enviar seus arquivos de log, os engenheiros de aplicativo de autenticação os usarão somente para solucionar problemas relatados pelo cliente.</li><li>Dados de uso não pessoalmente identificáveis, como "adicionar fluxo de conta iniciado/conta adicionada com êxito" ou "notificação aprovada." Esses dados são parte integrante de nossas decisões de engenharia. Seu uso nos ajuda a determinar onde podemos melhorar os aplicativos de maneiras que são importantes para você. Você verá uma notificação dessa coleta de dados quando usar o aplicativo pela primeira vez. Ele informa que ele pode ser desativado na página de **configurações** do aplicativo. Você pode ativar ou desativar essa configuração a qualquer momento.</li></ul>

### <a name="codes-in-the-app"></a>Códigos no aplicativo

**P**: quais são os códigos no aplicativo?

**R**: ao abrir o autenticador, você verá suas contas adicionadas como blocos. Suas contas corporativas ou de estudante e suas contas pessoais da Microsoft terão seis ou oito dígitos visíveis na exibição de tela inteira da conta (acessada ao tocar no bloco da conta). Para outras contas, você verá um número de seis ou oito dígitos na página **contas** do aplicativo.<br>Você usará esses códigos como senha de uso único para verificar se você está dizendo que está. Depois de entrar com seu nome de usuário e senha, digite o código de verificação associado a essa conta. Por exemplo, se você estiver Katy entrando em sua conta da Contoso, toque no bloco da conta e, em seguida, use o código de verificação 895823. Para a conta do Outlook, você seguirá as mesmas etapas.<br>Toque no bloco conta da contoso.<br>![Blocos de conta no aplicativo autenticador](media/user-help-auth-app-faq/katy-signin.png)<br>Depois de tocar no bloco da conta contoso, o código de verificação ficará visível em tela inteira.<br>![Código de verificação no bloco da conta no autenticador](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Contador de contagem regressiva

**P**: por que o número ao lado do código mantém a contagem inativa?

**R**: você pode ver um temporizador de 30 segundos contando ao lado do código de verificação ativo. Esse cronômetro é para que você nunca faça login usando o mesmo código duas vezes. Ao contrário de uma senha, não queremos que você se lembre desse número. A ideia é que apenas alguém com acesso ao seu telefone conheça o seu código.

### <a name="grayed-account-tile"></a>Bloco de conta esmaecida

**P**: por que meu bloco de conta está cinza?

**R**: algumas organizações exigem que o autenticador funcione com o logon único e proteja os recursos organizacionais. Nessa situação, a conta não é usada para verificação em duas etapas e aparece em cinza ou inativa. Esse tipo de conta é frequência chamado de conta de "corretor".

### <a name="device-registration"></a>Registro de dispositivos

**P**: o que é o registro de dispositivo?
**R**: sua organização pode exigir que você registre o dispositivo para controlar o acesso a recursos protegidos, como arquivos e aplicativos. Ela também pode ativar o acesso condicional para reduzir o risco de acesso indesejado a esses recursos. Você pode cancelar o registro do seu dispositivo em **Configurações**, mas poderá perder o acesso a e-mails no Outlook, arquivos no OneDrive e perderá a capacidade de usar o login por telefone.

### <a name="verification-codes-when-connected"></a>Códigos de verificação quando conectados

**P**: preciso estar conectado à Internet ou à minha rede para obter e usar os códigos de verificação?

**R**: os códigos não exigem que você esteja na Internet ou conectados a dados, portanto, você não precisa do serviço de telefone para entrar. Além disso, como o aplicativo para de funcionar assim que você fechá-lo, ele não irá drenar sua bateria.

### <a name="no-notifications-when-app-is-closed"></a>Nenhuma notificação quando o aplicativo é fechado

**P**: por que só obtenho notificações quando o aplicativo está aberto? Quando o aplicativo é fechado, não obtenho notificações.

**R**: se você estiver recebendo notificações, mas não um alerta, mesmo com o toque ativado, verifique as configurações do aplicativo. Certifique-se de que o aplicativo esteja ativado para usar som ou vibrar para notificações. Se você não receber notificações, verifique as seguintes condições:<ul><li>Seu telefone está no modo Silencioso ou Não Incomodar? Esses modos podem impedir que aplicativos enviem notificações.</li><li>Você pode obter notificações de outros aplicativos? Caso contrário, pode ser um problema com as conexões de rede no seu telefone ou com o canal de notificações do Android ou da Apple. Você pode tentar resolver suas conexões de rede por meio de suas configurações de telefone. Talvez seja necessário conversar com seu provedor de serviços para ajudar com o canal de notificações do Android ou da Apple.</li><li>Você consegue receber notificações de algumas contas no aplicativo, mas não em outras? Se sim, remova a conta problemática do seu aplicativo, adicione-a novamente permitindo notificações e veja se isso resolve o problema.</li></ul>Se você tentou todas essas etapas e ainda está tendo problemas, recomendamos enviar seus arquivos de log para diagnósticos. Abra o aplicativo, vá para **Ajuda** e selecione **Enviar logs**. Depois disso, vá para o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) e conte-nos o problema que você está vendo e as etapas que você tentou.

### <a name="switch-to-push-notifications"></a>Alternar para notificações por push

**P**: estou usando os códigos de verificação no aplicativo, mas como faço para alternar para as notificações por push?

**R**: você pode configurar notificações para sua conta corporativa ou de estudante (se permitido pelo administrador) ou para sua conta Microsoft pessoal. As notificações não funcionarão para contas de terceiros, como Google ou Facebook.<br>Para mudar sua conta pessoal para notificações, você precisará registrar novamente seu dispositivo com a conta. Vá para **Adicionar Conta**, selecione **Conta Microsoft Pessoal** e, em seguida, entre usando seu nome de usuário e senha.<br>Para sua conta corporativa ou de estudante, sua organização decide se deseja ou não permitir notificações com um clique.

### <a name="notifications-for-other-accounts"></a>Notificações para outras contas

**P**: as notificações funcionam para contas que não são da Microsoft?

**R**: não, as notificações só funcionam com contas da Microsoft e contas de Azure Active Directory. Se seu trabalho ou escola usa contas do Azure AD, eles são capazes de desativar esse recurso.

### <a name="backup-and-recovery"></a>Backup e recuperação

**P**: Eu recebi um novo dispositivo ou restaurei meu dispositivo de um backup. Como fazer configurar minhas contas no autenticador novamente?

**R**: se você ativou o **backup na nuvem** em seu dispositivo antigo, poderá usar o backup antigo para recuperar as credenciais da conta no novo Ios ou em um dispositivo Android. Para obter mais informações, consulte o artigo [fazer backup e recuperar credenciais de conta com o autenticador](user-help-auth-app-backup-recovery.md) .

### <a name="lost-device"></a>Dispositivo perdido

**P**: perdi meu dispositivo ou movi para um novo dispositivo. Como garantir que as notificações não continuem indo para o dispositivo antigo?

**R**: Adicionar autenticador ao novo dispositivo não remove automaticamente o aplicativo do dispositivo antigo. Até mesmo excluir o aplicativo do dispositivo antigo não é suficiente. Você deve excluir o aplicativo do dispositivo antigo e informar à Microsoft ou à sua organização para esquecer e cancelar o registro do dispositivo antigo.<ul><li>**Para remover o aplicativo de um dispositivo usando uma conta pessoal da Microsoft.** Vá para a área de verificação em duas etapas da página [Segurança da Conta](https://account.microsoft.com/security)  e escolha desativar a confirmação do dispositivo antigo.</li><li>**Para remover o aplicativo de um dispositivo usando uma conta corporativa ou de estudante da Microsoft.** Vá para a área de verificação em duas etapas da sua [página myapps](https://myapps.microsoft.com/) ou do portal personalizado da sua organização para desativar a verificação para o dispositivo antigo.</li></ul>

### <a name="remove-account-from-app"></a>Remover conta do aplicativo

**P**: como fazer remover uma conta do aplicativo?

**R**: toque no bloco da conta para a conta que você deseja remover do aplicativo para exibir a conta em tela inteira. Toque em **remover conta** para remover a conta do aplicativo.<br>Se você tiver um dispositivo registrado em sua organização, talvez precise de uma etapa extra para remover sua conta. Nesses dispositivos, o autenticador é registrado automaticamente como um administrador do dispositivo. Se você quiser desinstalar completamente o aplicativo, você precisa primeiro cancelar o registro do aplicativo nas configurações do aplicativo.

### <a name="too-many-permissions"></a>Excesso de permissões

**P**: por que o aplicativo solicita tantas permissões?

**R**: aqui está a lista completa de permissões que podem ser solicitadas e como elas são usadas pelo aplicativo. As permissões específicas que você verá dependem do tipo de telefone que você tem.<ul><li>**Usar hardware biométrico.** Algumas contas do trabalho e da escola exigem um PIN adicional sempre que você confirmar sua identidade. O aplicativo exige seu consentimento para usar o reconhecimento biométrico ou facial em vez de um PIN.</li><li>**Câmera.** Usado para digitalizar códigos QR quando você adiciona uma conta de trabalho, escola ou não-Microsoft.</li><li>**Contatos e telefone.** O aplicativo requer essa permissão para pesquisar contas corporativas ou de estudante da Microsoft em seu telefone e adicioná-las ao aplicativo para você.</li><li>**SMS.** Usado para garantir que seu número de telefone corresponda ao número no registro quando você entra com seu conta Microsoft pessoal pela primeira vez. Enviamos uma mensagem de texto para o telefone no qual você instalou o aplicativo que inclui um código de verificação de 6-8 dígitos. Você não precisa encontrar esse código e inseri-lo porque o autenticador o encontra automaticamente na mensagem de texto.</li><li>**Desenhar sobre outros aplicativos.** A notificação que você obtém que verifica sua identidade também é exibida em qualquer outro aplicativo em execução.</li><li>**Receba dados da internet.** Essa permissão é necessária para o envio de notificações.</li><li>**Impedir que o telefone entre em suspensão.** Se você registrar seu dispositivo em sua organização, sua organização poderá alterar essa política em seu telefone.</li><li>**Controle de vibração.** Você pode escolher se deseja uma vibração sempre que receber uma notificação para confirmar sua identidade.</li><li>**Use o hardware de impressão digital.** Algumas contas do trabalho e da escola exigem um PIN adicional sempre que você confirmar sua identidade. Para facilitar o processo, permitimos que você use sua impressão digital em vez de digitar o PIN.</li><li> **Exibir conexões de rede.** Quando você adiciona uma conta da Microsoft, o aplicativo requer conexão de rede / internet.</li><li>**Ler o conteúdo de seu armazenamento**. Essa permissão é usada apenas quando você relata um problema técnico por meio das configurações do aplicativo. Algumas informações de seu armazenamento são coletadas para diagnosticar o problema.</li><li>**Acesso total à rede.** Essa permissão é necessária para enviar notificações para verificar sua identidade.</li><li>**Execute na inicialização.** Se você reiniciar o smartphone, essa permissão garante que você continue recebendo notificações para confirmar sua identidade.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Aprovar solicitações sem desbloquear

**P**: por que o autenticador permite que você aprove uma solicitação sem desbloquear o dispositivo?

**R**: você não precisa desbloquear seu dispositivo para aprovar solicitações de verificação porque tudo o que você precisa provar é que você tem seu telefone com você. A verificação em duas etapas requer provar duas coisas, algo que você sabe, e algo que você tem. Algo que você sabe é a sua senha. O que você tem é seu telefone (configurado com o autenticador e registrado como uma prova de autenticação multifator.) Portanto, ter o telefone e aprovar a solicitação atende aos critérios do segundo fator de autenticação.

### <a name="activity-notifications"></a>Notificações de atividade

**P**: por que estou recebendo notificações sobre a atividade da minha conta?

**R**: as notificações de atividade são enviadas para o autenticador imediatamente sempre que uma alteração é feita em suas contas pessoais da Microsoft, ajudando a manter você mais seguro. Anteriormente, enviamos essas notificações somente por email e SMS. Para obter mais informações sobre essas notificações de atividade, confira [O que acontece se houver uma entrada incomum na sua conta](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Para alterar o local em que você recebe suas notificações, entre na página [Para onde podemos enviar alertas de conta não críticos?](https://account.live.com/SecurityNotifications/Update) da sua conta.

### <a name="one-time-passcodes"></a>Senhas de uso único

**P**: minhas senhas de uso único não estão funcionando. O que devo fazer?

**R**: Verifique se a data e a hora em seu dispositivo estão corretas e estão sendo sincronizadas automaticamente. Se a data e a hora estiverem erradas ou fora de sincronia, o código não funcionará.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**P**: o sistema operacional Windows 10 Mobile foi preterido em dezembro de 2019. O Microsoft Authenticator em sistemas operacionais Windows Mobile também será preterido?

**R: o** autenticador em todos os sistemas operacionais Windows Mobile não terá suporte após 28 de fevereiro de 2020. Os usuários não estarão qualificados para receber novas atualizações do aplicativo após a data mencionada acima. Após 28 de fevereiro de 2020, os serviços da Microsoft que atualmente dão suporte a autenticações que usam o Microsoft Authenticator em todos os sistemas operacionais Windows Mobile começarão a ter o suporte desativado. Para autenticar nos serviços da Microsoft, recomendamos que todos os nossos usuários mudem para outro mecanismo de autenticação antes dessa data.

### <a name="default-mail-app"></a>Aplicativo de email padrão

**P**: ao entrar em minha conta corporativa ou de estudante usando o aplicativo de email padrão que vem com o Ios, recebo uma solicitação pelo autenticador para minhas informações de verificação de segurança. Depois de inserir essas informações e retornar ao aplicativo de email, recebo um erro. O que posso fazer?

**R**: isso provavelmente acontece porque sua entrada e seu aplicativo de email estão ocorrendo em dois aplicativos diferentes, fazendo com que o processo inicial de entrada em segundo plano pare de funcionar e falhe. Para tentar corrigir esse problema, recomendamos selecionar o ícone **Safari** no canto inferior direito da tela ao entrar no seu aplicativo de email. Ao mudar para o Safari, todo o processo de entrada ocorre em um único aplicativo, permitindo entrar no aplicativo com êxito.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**P**: por que estou tendo problemas com Apple Watch no watchOS 7?

**R**: há um problema com a aprovação de notificações no watchOS 7, e estamos trabalhando com a Apple para corrigir isso. Enquanto isso, todas as notificações que exigem o aplicativo Microsoft Authenticator watchOS devem ser aprovadas em seu telefone.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch não mostra contas

**P**: por que todas as minhas contas não aparecem quando abro o autenticador no meu Apple Watch?

**R: o** autenticador dá suporte apenas a contas pessoais ou de estudante ou corporativas da Microsoft com notificações por push no aplicativo Apple Watch Companion. Para suas outras contas, como o Google ou o Facebook, você precisa abrir o aplicativo autenticador em seu telefone para ver seus códigos de verificação.

### <a name="apple-watch-notifications"></a>Notificações de Apple Watch

**P**: por que não posso aprovar ou negar notificações no meu Apple Watch?

**R**: primeiro, verifique se você atualizou para autenticar a versão 6.0.0 ou superior no seu iPhone. Depois disso, abra o aplicativo complementar Microsoft Authenticator no seu Apple Watch e procure as contas com o botão **Configurar** abaixo delas. Conclua o processo de instalação para aprovar as notificações para essas contas.

### <a name="apple-watch-communication-error"></a>Erro de comunicação de Apple Watch

**P**: Estou recebendo um erro de comunicação entre o Apple Watch e o meu telefone. O que posso fazer para solucionar problemas?

**R**: esse erro ocorre quando a tela de inspeção passa para o estado de suspensão antes de concluir a comunicação com o telefone.<br><b>Se o erro ocorrer durante a instalação:</b><br>Tente executar a instalação novamente, certificando-se de manter seu relógio ativo até que o processo ser concluído. Ao mesmo tempo, abra o aplicativo em seu telefone e responda a qualquer prompt que aparecer.<br>Se o telefone e o relógio ainda não estiverem se comunicando, você poderá tentar as seguintes ações:<ol><li>Forçar encerrar o aplicativo do telefone Microsoft Authenticator e abri-lo novamente no seu iPhone.</li><li>Forçar o encerramento do aplicativo complementar no seu Apple Watch.<ol><li> Abra o aplicativo complementar Microsoft Authenticator no seu relógio</li><li>Mantenha pressionado o botão de lado até que o **desligamento** tela é exibida.</li><li>Solte o botão lateral e segure a Coroa Digital para forçar a saída do aplicativo ativo.</li></ol></li><li>Desligue o Bluetooth e o Wi-Fi no telefone e no relógio e ligue-os novamente.</li><li>Reinicie seu iPhone e seu relógio.</li></ol><b>Se o erro ocorrer quando você estiver tentando aprovar uma notificação:</b><br>Na próxima vez que tentar aprovar uma notificação em seu Apple Watch, mantenha a tela ativo até que a solicitação é concluída e você ouvir o som que indica que ela foi bem-sucedida.

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch aplicativo complementar não está sincronizando

**P**: por que o aplicativo Microsoft Authenticator Companion não é Apple Watch a sincronização ou exibição no meu relógio?

**R**: se o aplicativo não estiver aparecendo em seu relógio, tente as seguintes ações: <ol><li>Certifique-se de que o seu relógio está a vigiar o watchOS 4.0 ou superior.</li><li>Sincronize seu relógio novamente.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch aplicativo complementar falhou

**P**: meu aplicativo Apple Watch Companion falhou. Posso enviar meus logs de falhas para você investigar?

**R**: primeiro você precisa ter certeza de que optou por compartilhar sua análise conosco. Se você for um usuário de TestFlight, você já se inscreveu. Caso contrário, você pode ir para **Configurações > privacidade > Análise** e selecione o **compartilham o iPhone e análise de inspeção** e o **compartilhamento com desenvolvedores de aplicativos** opções.<br>Depois de se inscrever, você pode tentar reproduzir a falha para que os logs de falhas sejam enviados automaticamente para investigação. No entanto, se você não conseguir reproduzir a falha, poderá copiar manualmente os arquivos de log e enviá-los para nós.<ol><li>Abra o aplicativo Watch no seu smartphone, acesse **Configurações> Geral** e clique em **Copiar Analítica de exibição**.</li><li>Encontre a falha correspondente em **Configurações> Privacidade> Analytics> Dados do Analytics** e copie manualmente todo o texto.</li><li>Abra o autenticador em seu telefone e cole o texto copiado na caixa de texto **compartilhar com desenvolvedores de aplicativos** na página **enviar logs** .</li></ol>

## <a name="autofill-for-consumers"></a>Preenchimento automático para consumidores

**P**: o que é o preenchimento automático no autenticador?

**R**: o aplicativo autenticador agora armazena e preenche com segurança as senhas em aplicativos e sites que você visita em seu telefone. Você pode usar o preenchimento automático para sincronizar e fazer o preenchimento automático de suas senhas em seus dispositivos iOS e Android. Depois de configurar o aplicativo autenticador como um provedor de preenchimento automático em seu telefone, ele oferece para salvar suas senhas quando elas são inseridas em uma página de entrada do site ou do aplicativo. As senhas são salvas como parte de [seu conta Microsoft](https://account.microsoft.com/account) e também estão disponíveis quando você entra no Microsoft Edge com seu conta Microsoft.

**P**: quais informações o AutoPreenchimento pode autenticar para mim?

**R: o** autenticador pode AutoPreenchimento de nomes de logon e senhas em sites e aplicativos que você visita em seu telefone.

**P**: como fazer ativar o preenchimento automático de senha no autenticador em meu telefone?

**R**: Siga estas etapas:

1. Abra o aplicativo autenticador.
1. Em **configurações** em **beta**, ative o **preenchimento automático**.
1. Na guia **senhas** no autenticador, selecione **entrar com a conta da Microsoft** e entre usando [seu conta Microsoft](https://account.microsoft.com/account). Esse recurso atualmente dá suporte apenas a contas da Microsoft e ainda não oferece suporte a contas corporativas ou de estudante.

**P**: como fazer tornar autenticador o provedor de Autopreenchimento padrão em meu telefone?

**R**: Siga estas etapas:

1. Abra **as configurações** do autenticador e, em **beta** , ative o **preenchimento automático**.
1. Na guia **senhas** dentro do aplicativo, entre usando [seu conta Microsoft](https://account.microsoft.com/account).
1. Realize um dos seguintes procedimentos:

   - No iOS, em **configurações**, selecione **como ativar o preenchimento automático** na seção Configurações de preenchimento automático para saber como definir o autenticador como o provedor de preenchimento automático padrão.
   - No Android, em **configurações**, selecione **definir como provedor de preenchimento automático** na seção Configurações de preenchimento automático para definir o autenticador como o provedor de Autopreenchimento padrão.

**P**: e se a opção de **preenchimento automático** estiver esmaecida para mim em configurações?

**R: o** preenchimento automático está atualmente em beta e ainda não foi habilitado para todas as organizações ou tipos de conta. Se a opção de **preenchimento automático** em **configurações** estiver esmaecida para você, é provável que você esteja usando o aplicativo autenticador com sua conta corporativa. Você pode usar esse recurso em um dispositivo em que sua conta corporativa não é adicionada. Se sua organização trabalha com a Microsoft, a opção de **preenchimento automático** será habilitada mesmo quando uma conta de trabalho for adicionada ao autenticador.

**P**: como fazer parar de sincronizar senhas?

**R**: para interromper a sincronização de senhas no aplicativo autenticador, abra **configurações**  >  **preenchimento automático configurações**  >  **conta de sincronização**. Na próxima tela, você pode selecionar em **parar sincronização e remover todos os dados de preenchimento automático**. Isso removerá senhas e outros dados de preenchimento automático do dispositivo. A remoção de dados de preenchimento automático não afeta a autenticação multifator.

**P**: como as minhas senhas são protegidas pelo aplicativo autenticador?

**R: o** aplicativo autenticador já fornece um alto nível de segurança para autenticação multifator e gerenciamento de conta, e a mesma barra de segurança alta também é estendida para gerenciar suas senhas.

- A **autenticação forte é necessária para o aplicativo autenticador**: entrar no autenticador requer um segundo fator. Isso significa que suas senhas dentro do aplicativo autenticador não podem ser acessadas mesmo que alguém tenha sua senha de conta Microsoft.
- **Os dados de preenchimento automático são protegidos com biometria e** senha: antes de poder reativar o preenchimento da senha em um aplicativo ou site, o autenticador requer biométrica ou senha do dispositivo. Isso garante que, mesmo que outra pessoa tenha acesso ao seu dispositivo, ele não poderá preencher ou ver sua senha, pois não seria possível fornecer a biométrica ou o PIN do dispositivo. Além disso, um usuário não pode abrir a página senhas, a menos que forneça biométrica ou PIN, mesmo que desativem o bloqueio de aplicativo nas configurações do aplicativo.
- **Senhas criptografadas no dispositivo**: as senhas no dispositivo são criptografadas e as chaves de criptografia/descriptografia nunca são armazenadas e sempre geradas imediatamente. As senhas são descriptografadas apenas quando o usuário deseja, ou seja, durante o preenchimento automático ou quando o usuário deseja ver a senha, ambas exigindo biométrica ou PIN.
- **Segurança de rede e nuvem**: suas senhas na nuvem são criptografadas e descriptografadas somente quando atingem seu dispositivo. As senhas são sincronizadas em uma conexão HTTPS protegida por SSL, o que garante que nenhum invasor possa bisbilhotar dados confidenciais quando ele estiver sendo sincronizado. Também garantimos que verificamos a sanidade dos dados que estão sendo sincronizados pela rede usando funções com hash criptografado (especificamente, o código de autenticação de mensagens baseado em hash).

## <a name="autofill-for-it-admins"></a>Preenchimento automático para administradores de ti

**P**: meus funcionários ou alunos terão que usar o preenchimento automático de senha no aplicativo autenticador?

**R**: não. O recurso de preenchimento automático está atualmente em beta e ainda não foi habilitado para todas as organizações ou tipos de conta. Se seu funcionário ou aluno tiver adicionado sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator, o preenchimento automático de senhas não estará acessível a eles. A única exceção a essa restrição é quando seu funcionário ou aluno adiciona sua conta corporativa ou de estudante à autenticação multifator baseada em nuvem da Microsoft como uma [conta externa ou](user-help-auth-app-add-non-ms-account.md)de terceiros.

**P**: posso disponibilizar o recurso de preenchimento automático para meus funcionários (ou alunos)?

**R**: Sim. Para habilitar seus funcionários ou alunos, sua empresa ou escola pode ser adicionada a uma lista de permissões. Contate seu suporte ou contato da Microsoft para ser adicionado à lista de permissões. Além disso, se você for um administrador de ti para sua organização, também poderá preencher um formulário para expressar seu interesse em ingressar em uma [empresa de permissão para preenchimento automático no autenticador](https://aka.ms/RequestAutofillInAuthenticator).

**P**: a senha da conta corporativa ou de estudante do meu usuário será sincronizada automaticamente?

**R**: não. O preenchimento automático de senha não sincronizará a senha da conta corporativa ou de estudante para seus usuários. Quando os usuários visitam um site ou um aplicativo, o autenticador oferecerá para salvar a senha para esse site ou aplicativo, e a senha será salva somente quando o usuário escolher.
  
**P**: posso permitir que liste somente determinados usuários da minha organização para preenchimento automático?

**R**: não. As empresas só podem habilitar o preenchimento automático de senhas para todos ou nenhum de seus funcionários no momento. Iremos expandir esses controles gradualmente.

**P**: e se meu funcionário ou aluno tiver várias contas corporativas ou de estudante? Por exemplo, meu funcionário tem contas de várias empresas ou escolas em seus Microsoft Authenticator.

**R**: todas as empresas ou escolas adicionadas no aplicativo autenticador precisam ser permitidas-listadas para preenchimento automático no autenticador para o proprietário do aplicativo poder usá-lo. A única exceção a essa restrição é quando seu funcionário ou aluno adiciona sua conta corporativa ou de estudante à autenticação multifator baseada em nuvem da Microsoft como uma [conta externa ou](user-help-auth-app-add-non-ms-account.md)de terceiros.

## <a name="next-steps"></a>Próximas etapas

- Se estiver com problemas para receber o código de verificação da sua conta pessoal da Microsoft, consulte a seção **Solucionar problemas de código de verificação** do artigo [Informações e códigos de verificação de segurança das contas da Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Se você quiser mais informações sobre a verificação em duas etapas, consulte [Configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se quiser mais informações sobre segurança, consulte [Visão geral das informações de segurança (versão preliminar)](./security-info-setup-signin.md)

- Se sua pergunta não tiver sido respondida aqui, queremos ouvir sua opinião. Acesse o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) para postar a pergunta e obter ajuda da comunidade ou deixe um comentário nesta página.
