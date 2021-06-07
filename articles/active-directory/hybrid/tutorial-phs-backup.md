---
title: 'Tutorial:  Configurando o PHS como backup para o AD FS no Azure AD Connect | Microsoft Docs'
description: Demonstra como ativar a sincronização de hash de senha como um backup e para o AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313086"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial:  Configurando o PHS como backup para o AD FS no Azure AD Connect

O tutorial a seguir orientará você pela configuração da sincronização de hash de senha como um backup e failover para o AD FS.  Este documento também demonstrará como habilitar a sincronização de hash de senha como o método de autenticação primário em caso de falha ou de não disponibilidade do AD FS.

>[!NOTE] 
>Embora essas etapas geralmente sejam executadas durante situações de emergência ou de interrupção, é recomendável testá-las e verificar seus procedimentos antes que uma interrupção ocorra.

>[!NOTE]
>Se você não tem acesso ao servidor do Azure AD Connect ou se ele não tem acesso à Internet, contate o [Suporte da Microsoft](https://support.microsoft.com/en-us/contactus/) para ajudá-lo com as alterações no lado do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos
Esse tutorial é baseado no [Tutorial: Federar um ambiente com uma única floresta do AD na nuvem](tutorial-federation.md) e é um pré-requisito antes de tentar realizar este tutorial.  Se você não tiver concluído este tutorial, faça isso antes de tentar realizar as etapas neste documento.

>[!IMPORTANT]
>Antes de mudar para o PHS, você deve criar um backup do seu ambiente do AD FS.  Isso pode ser feito usando a [Ferramenta de Restauração Rápida do AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Habilitar o PHS no Azure AD Connect
A primeira etapa, agora que temos um ambiente do Azure AD Connect que está usando a federação, é ativar a sincronização de hash de senha e permitir que o Azure AD Connect sincronize os hashes.

Faça o seguinte:

1.  Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Na página Tarefas adicionais, selecione **Personalizar opções de sincronização** e clique em **Avançar**.
4.  Insira o nome de usuário e a senha do seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na tela **Conectar seus diretórios**, clique em **Avançar**.
6.  Na tela **Filtragem de Domínio e UO**, clique em **Avançar**.
7.  Na tela de **Recursos opcionais**, marque a **Sincronização de hash de senha** e clique em **Avançar**.
![Selecionar](media/tutorial-phs-backup/backup1.png)</br>
8.  Na tela **Pronto para configurar**, clique em **Configurar**.
9.  Após a configuração ser concluída, clique em **Sair**.
10. É isso!  Pronto.  A sincronização de hash de senha ocorrerá agora e poderá ser usada como backup em caso de não disponibilidade do AD FS.

## <a name="switch-to-password-hash-synchronization"></a>Alternar para a sincronização de hash de senha
Agora, mostraremos a você como alternar para a sincronização de hash de senha. Antes de começar, considere em quais circunstâncias você deve fazer essa mudança. Não faça a mudança por motivos temporários, como uma interrupção de rede, um pequeno problema do AD FS ou um problema que afeta um subconjunto de seus usuários. Se decidir fazer a mudança porque a correção do problema será muito demorada, faça o seguinte:

> [!IMPORTANT]
> Lembre-se de que levará um instante para os hashes de senha serem sincronizados com o Azure AD.  Isso significa que pode levar até 3 horas para que as sincronizações sejam concluídas e antes que você possa começar a autenticar-se usando os hashes de senha.

1. Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Alterar a entrada do usuário** e clique em **Avançar**.
![Alteração](media/tutorial-phs-backup/backup2.png)</br>
4.  Insira o nome de usuário e a senha do seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na tela **Entrada do usuário**, selecione **Sincronização de hash de senha** e marque a caixa **Não converter contas de usuário**.  
6.  Deixe a opção padrão **Habilitar o logon único** selecionada e clique em **Avançar**.
7.  Na tela **Habilitar o logon único**, clique em **Avançar**.
8.  Na tela **Pronto para configurar**, clique em **Configurar**.
9.  Quando a configuração estiver concluída, clique em **Sair**.
10. Os usuários agora podem usar suas senhas para entrar no Azure e nos serviços do Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Testar entrando com um dos usuários

1. Navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Entre com uma conta de usuário que foi criada no novo locatário.  Será necessário entrar usando o formato a seguir: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para entrar localmente.</br>
   ![Captura de tela que mostra uma mensagem de sucesso ao testar a conexão. ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Volte para a federação
Agora mostraremos como voltar para a federação.  Para fazer isso, execute o seguinte:

1.  Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Alterar a entrada do usuário** e clique em **Avançar**.
4.  Insira o nome de usuário e a senha do seu administrador global.  Esta é a conta que foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na tela **Conexão do usuário**, selecione **Federação com o AD FS** e clique em **Avançar**.  
6. Na página de credenciais do Administrador de Domínios, insira o nome de usuário e a senha do Administrator/contoso e clique em **Avançar.**
7. Na tela de farm do AD FS, clique em **Avançar**.
8. Na tela **domínio do Azure AD**, selecione o domínio na lista suspensa e clique em **Avançar**.
9. Na tela **Pronto para configurar**, clique em **Configurar**.
10. Depois que a configuração for concluída, clique em **Avançar**.
![Configurar](media/tutorial-phs-backup/backup4.png)</br>
11. Na tela **Verificar conectividade da federação**, clique em **Verificar**.  Talvez seja necessário configurar registros DNS (adicionar registros A e AAAA) para que isso seja concluído com êxito.
![Captura de tela que mostra a tela Verificar conectividade da federação e o botão Verificar.](media/tutorial-phs-backup/backup5.png)</br>
12. Clique em **Sair**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Redefinir a confiança do AD FS e do Azure
Agora é necessário redefinir a confiança entre o AD FS e o Azure.

1.  Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Gerenciar Federação** e clique em **Avançar**.
4.  Selecione **Redefinir confiança do Azure AD** e clique em **Avançar**.
![Redefinir](media/tutorial-phs-backup/backup6.png)</br>
5.  Na tela **Conectar-se ao Azure AD**, insira o nome de usuário e a senha do seu administrador global.
6.  Na tela **Conectar-se ao AD FS**, insira o nome de usuário e a senha de contoso\Administrator e clique em **Avançar.**
7.  Na tela **Certificados**, clique em **Avançar**.

## <a name="test-signing-in-with-a-user"></a>Testar a entrada com um usuário

1.  Navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Entre com uma conta de usuário que foi criada no novo locatário.  Será necessário entrar usando o formato a seguir: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para entrar no local.
![Verificar](media/tutorial-password-hash-sync/verify1.png)

Agora você configurou com êxito um ambiente de identidade híbrida que pode ser usado para testar e familiarizar-se com o que o Azure tem a oferecer.

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)