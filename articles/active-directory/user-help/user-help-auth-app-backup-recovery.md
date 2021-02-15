---
title: Fazer backup e recuperar contas com o aplicativo Microsoft Authenticator – Azure AD
description: Saiba como fazer backup e recuperar as credenciais da sua conta em backup usando o aplicativo Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530894"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais de conta usando o aplicativo Microsoft Authenticator

**Aplica-se a:**

- Dispositivos iOS que executem a versão 5.7.0 e posterior

- Dispositivos Android que executem a versão 6.6.0 e posterior

O aplicativo Microsoft Authenticator faz backup das credenciais da sua conta e das configurações de aplicativos relacionadas, como a ordem de suas contas, na nuvem. Após o backup, também será possível usar o aplicativo para recuperar as informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

Cada local de armazenamento de backup exige que você tenha um conta Microsoft pessoal, enquanto o iOS também exige uma conta no iCloud. Você pode ter várias contas armazenadas nesse único local. Por exemplo, você pode ter uma conta pessoal, uma conta corporativa ou de estudante e uma conta pessoal que não seja da Microsoft, como Facebook, Google e assim por diante.

> [!IMPORTANT]
> Somente as credenciais da conta pessoal e de terceiros são armazenadas, o que inclui o nome de usuário e o código de verificação de conta para provar sua identidade. Não armazenamos outras informações associadas às contas, incluindo emails ou arquivos. Também não associamos nem compartilhamos as contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, o administrador de TI não receberá informações sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup das credenciais da conta

Antes de fazer backup das credenciais, será necessário:

- Uma [conta pessoal da Microsoft](https://account.microsoft.com/account) para agir como uma conta de recuperação.

- **Apenas para iOS,**  você deve ter uma [conta iCloud](https://www.icloud.com/) para o local de armazenamento real.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Ativar o backup na nuvem para dispositivos iOS

- Em seu dispositivo iOS, selecione **Configurações**, **Backup** e, em seguida, ative **Backup do iCloud**.

    O backup das credenciais da sua conta é feito para a conta do iCloud.

    ![Tela de configurações do iOS mostrando a localização das configurações de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Ativar o backup na nuvem para dispositivos Android

- Em seu dispositivo Android, selecione **Configurações** e **Backup**, depois ative o **Backup na Nuvem**.

    O backup das credenciais da sua conta é feito na conta em nuvem.

    ![Tela de configurações do Android, mostrando o local das configurações de backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

Você pode recuperar as credenciais da sua conta em sua conta na nuvem, mas deve primeiro verificar se a conta que você está recuperando não existe no aplicativo Microsoft Authenticator. Por exemplo, se você estiver recuperando sua conta Microsoft pessoal, certifique-se de que você não tenha uma conta desse tipo já configurada no aplicativo de autenticação. Essa verificação é importante para que possamos ter certeza de que não estamos substituindo ou apagando uma conta existente por engano.

### <a name="to-recover-your-information"></a>Para recuperar as informações

1. No dispositivo móvel, abra o aplicativo Microsoft Authenticator e selecione **Iniciar a recuperação** na parte inferior da tela.

    ![Aplicativo Microsoft Authenticator, mostrando onde clicar em Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Entre na conta de recuperação, usando a mesma conta pessoal da Microsoft utilizada durante o processo de backup.

    As credenciais da conta são recuperadas para o novo dispositivo.

Após concluir a recuperação, você poderá notar que os códigos de verificação de conta pessoal da Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam durante a autenticação usando o telefone associado.

## <a name="recover-accounts-requiring-more-verification"></a>Recuperar contas que exigem mais verificação

Se você usar notificações por push com contas pessoais, corporativas ou de estudante, um alerta será exibido na tela informando que é necessário fornecer uma verificação adicional para poder recuperar suas informações. Como as notificações por push exigem o uso de uma credencial vinculada ao dispositivo específico e nunca enviada pela rede, você deverá provar sua identidade antes que a credencial seja criada no dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo a senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deverá digitalizar um código QR fornecido pelo provedor de conta.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Para fornecer mais verificação para contas pessoais

1. Na tela **contas** do aplicativo Microsoft Authenticator, toque na conta que você deseja recuperar para abrir a exibição de tela inteira da conta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Captura de tela que mostra o aplicativo Microsoft Authenticator com os blocos de conta disponíveis." border="true":::

1. Toque no bloco da conta que você está recuperando e, em seguida, toque na opção para entrar para recuperar. Insira sua senha e confirme seu endereço de email ou número de telefone como verificação adicional.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Captura de tela que mostra o aplicativo Microsoft Authenticator com os blocos de conta disponíveis." border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Para fornecer mais verificação para contas corporativas ou de estudante

1. Na tela **contas** do aplicativo Microsoft Authenticator, toque na conta que você deseja recuperar para abrir a exibição de tela inteira da conta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Captura de tela que mostra o aplicativo Microsoft Authenticator com os blocos de conta disponíveis." border="true":::

1. Na exibição de tela inteira, toque na opção para digitalizar um código QR para se recuperar totalmente.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Captura de tela que mostra o aplicativo Microsoft Authenticator com os blocos de conta disponíveis." border="true":::

>[!NOTE]
>Para mais informações sobre códigos QR e como obtê-los, confira [Começar a usar o aplicativo Microsoft Authenticator](./user-help-auth-app-download-install.md) ou [Configurar informações de segurança para usar um aplicativo de autenticação](./security-info-setup-auth-app.md), dependendo de o administrador ter ativado ou não as informações de segurança.
>
>Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Solução de problemas de backup e recuperação

Há alguns motivos pelos quais o backup pode não estar disponível

- **Alteração de sistemas operacionais.** : o backup é armazenado no iCloud para iOS e no provedor de armazenamento em nuvem da Microsoft para Android. Isso significa que o backup não estará disponível se você alternar entre dispositivos Android e iOS. Se você alternar, deverá recriar manualmente suas contas no aplicativo Microsoft Authenticator.

- **Problemas de rede**: se você estiver enfrentando problemas relacionados à rede, verifique se está conectado à rede e se entrou corretamente na sua conta.

- **Problemas na conta**: se você estiver enfrentando problemas relacionados à conta, verifique se está conectado corretamente à sua conta. No iOS, isso significa que você precisa estar conectado ao iCloud usando a mesma conta AppleID que seu iPhone.

- **Exclusão acidental**: é possível que você tenha excluído a conta de backup do dispositivo anterior ou ao gerenciar a conta de armazenamento em nuvem. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

- **Contas existentes do Microsoft Authenticator**: Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar as contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, antes de recuperar o backup você deverá remover todas as informações de conta existentes das contas existentes configuradas no aplicativo Authenticator.

- **O backup está desatualizado**: caso suas informações de backup estejam desatualizadas, você pode ser solicitado a atualizar as informações entrando novamente na sua conta de recuperação da Microsoft. A conta de recuperação é a conta Microsoft pessoal usada inicialmente para armazenar o backup. Se uma entrada for necessária, você verá um ponto vermelho no menu ou na barra de ação, ou verá um ícone de sinal de exclamação solicitando que você entre para concluir a restauração do backup. Depois de selecionar o ícone apropriado, você será solicitado a entrar novamente para atualizar suas informações.

## <a name="next-steps"></a>Próximas etapas

Agora que você fez backup e recuperou as credenciais da conta para o novo dispositivo, continue usando o aplicativo Microsoft Authenticator para verificar a identidade. Para obter mais informações, confira [Entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Artigos relacionados

- [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)

- [Autenticação multifator](/azure/multi-factor-authentication/)