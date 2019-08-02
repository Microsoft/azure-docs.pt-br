---
title: Backup e recuperação com o aplicativo Microsoft Authenticator - Active Directory do Azure | Microsoft Docs
description: Saiba como fazer backup e recuperar as credenciais da sua conta usando o aplicativo Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4148d8a3b44336677ba028807aadbae424b7223
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382509"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais de conta com o aplicativo Microsoft Authenticator

**Aplica-se a:**

- Dispositivos iOS

O aplicativo Microsoft Authenticator faz backup das credenciais da sua conta e das configurações de aplicativos relacionadas, como a ordem de suas contas, na nuvem. Após o backup, também será possível usar o aplicativo para recuperar as informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

> [!IMPORTANT]
> É necessário ter uma conta pessoal da Microsoft e uma conta do iCloud para cada local de armazenamento de backup. Mas, dentro desse local de armazenamento será possível fazer o backup de várias contas. Por exemplo, você pode ter uma conta pessoal, uma conta de estudante e uma conta de terceiros como Facebook, Google, e assim por diante.
>
> Somente as credenciais da conta pessoal e de terceiros são armazenadas, o que inclui o nome de usuário e o código de verificação de conta para provar a sua identidade. Não armazenamos outras informações associadas às contas, incluindo emails ou arquivos. Também não associamos ou compartilhamos as contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, o administrador de TI não receberá informações sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup das credenciais da conta

Antes de fazer backup das credenciais, será necessário ter as duas contas a seguir:

- Uma [conta pessoal da Microsoft](https://account.microsoft.com/account) para agir como uma conta de recuperação.

- Uma [conta do iCloud](https://www.icloud.com/) para o local de armazenamento real.

A exigência de entrar em ambas as contas conjuntamente proporciona segurança mais forte para as informações de backup.

### <a name="to-turn-on-cloud-backup"></a>Para ativar o backup na nuvem

- Em seu dispositivo iOS, selecione **Configurações**, **Backup** e, em seguida, ative **Backup do iCloud**.

    O backup das credenciais da sua conta é feito para a conta do iCloud.

    ![Tela de configurações do iOS mostrando a localização das configurações de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

É possível recuperar as credenciais da conta do iCloud, usando a mesma conta de recuperação da Microsoft que você configurou ao fazer o backup das informações.

### <a name="to-recover-your-information"></a>Para recuperar as informações

1. No dispositivo iOS, abra o aplicativo Microsoft Authenticator e selecione **Iniciar a recuperação** na parte inferior da tela.

    ![Aplicativo Microsoft Authenticator, mostrando onde clicar em Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Entre na conta de recuperação, usando a mesma conta pessoal da Microsoft utilizada durante o processo de backup.

    As credenciais da conta são recuperadas para o novo dispositivo.

Após concluir a recuperação, você poderá notar que os códigos de verificação de conta pessoal da Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam durante a autenticação usando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que exigem mais verificação

Se você usar notificações por push com as contas pessoais, corporativa ou de estudante, um alerta será exibido na tela informando que será necessário fornecer uma verificação adicional para poder recuperar as informações. Como as notificações por push exigem o uso de uma credencial vinculada ao dispositivo específico e nunca enviada pela rede, você deverá provar sua identidade antes que a credencial seja criada no dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo a senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deverá digitalizar um código QR fornecido pelo provedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para contas pessoais

1. Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Selecione **Entrar para recuperar**, digite a senha e confirme o endereço de email ou o número de telefone como verificação adicional.

    ![Aplicativo Microsoft Authenticator, permitindo que você insira as informações de entrada](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para contas corporativas ou de estudante

1. Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Selecione **Digitalizar código QR para recuperar** e, em seguida, digitalize o código QR.

    ![Aplicativo Microsoft Authenticator, permitindo que você digitalize o código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para obter mais informações sobre como obter um código QR, consulte [Começar a usar o aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [Configurar informações de segurança para usar um aplicativo autenticador](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) dependendo de se o seu administrador ativou as informações de segurança.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Solução de problemas de backup e recuperação

Há alguns motivos pelos quais o backup pode não estar disponível:

- **Alterar sistemas operacionais.** O backup é armazenado na opção de armazenamento em nuvem fornecida pelo sistema operacional do telefone, o que significa que o backup não estará disponível se você alternar entre Android e iOS. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

- **Problemas de rede ou senha.** Certifique-se de estar conectado a uma rede e conectado à conta do iCloud usando o mesmo AppleID utilizado no último iPhone.

- **Exclusão acidental.** É possível que você tenha excluído a conta de backup do dispositivo anterior ou ao gerenciar a conta de armazenamento em nuvem. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

- **Contas existentes do Microsoft Authenticator.** Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar as contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, antes de recuperar o backup você deverá remover todas as informações de conta existentes das contas existentes configuradas no aplicativo Authenticator.

## <a name="next-steps"></a>Próximas etapas

Agora que você fez backup e recuperou as credenciais da conta para o novo dispositivo, continue usando o aplicativo Microsoft Authenticator para verificar a identidade. Para obter mais informações, consulte [entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Tópicos relacionados

- [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)

- [Autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/)
