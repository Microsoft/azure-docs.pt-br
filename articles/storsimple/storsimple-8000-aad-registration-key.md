---
title: Autenticação do Azure AD para o StorSimple 8000 no Gerenciador de Dispositivos
description: Explica como usar a autenticação baseada no AAD para o serviço, gerar uma nova chave de registro e realizar o registro manual dos dispositivos.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99805033"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Usar a autenticação do Azure Active Directory (AD) para o StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Visão geral

O serviço Gerenciador de Dispositivo do StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Até o momento, o serviço Gerenciador de Dispositivos do StorSimple usou um ACS (serviço de Controle de Acesso) para autenticar o serviço no dispositivo StorSimple. O mecanismo do ACS será preterido em breve e substituído por uma autenticação do AAD (Azure Active Directory). Para obter mais informações, acesse os comunicados a seguir sobre a reprovação do ACS e o uso da autenticação do AAD.

- [O futuro do Azure ACS é o Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Alterações futuras no Serviço de Controle de Acesso da Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Este artigo descreve os detalhes da autenticação do AAD e a nova chave de registro do serviço associada, bem como modificações nas regras de firewall aplicáveis aos dispositivos StorSimple. As informações contidas neste artigo aplicam-se apenas a dispositivos StorSimple da série 8000.

A autenticação do AAD ocorre em um dispositivo StorSimple da série 8000 que executa a Atualização 5 ou posterior. Devido à introdução da autenticação do AAD, ocorrem alterações em:

- Padrões de URL para regras de firewall.
- Chave de registro do serviço.

Essas alterações são abordadas em detalhes nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para a autenticação do AAD

Para garantir que o serviço use a autenticação baseada no AAD, todos os usuários precisam incluir as novas URLs de autenticação em suas regras de firewall.

Se estiver usando um StorSimple série 8000, verifique se a seguinte URL está incluída nas regras de firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos EUA |Serviço de autenticação do AAD      |

Para obter uma lista completa de padrões de URL para dispositivos StorSimple da série 8000, acesse [Padrões de URL para regras de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se a URL de autenticação não estiver incluída nas regras de firewall além da data de reprovação e o dispositivo estiver executando a Atualização 5, os usuários verão um alerta de URL. Os usuários precisam incluir a nova URL de autenticação. Se o dispositivo estiver executando uma versão anterior à Atualização 5, os usuários verão um alerta de pulsação. Em cada caso, o dispositivo StorSimple não pode ser autenticado no serviço e o serviço não pode se comunicar com o dispositivo.

## <a name="device-version-and-authentication-changes"></a>Alterações de versão e autenticação de dispositivo

Se estiver usando um dispositivo StorSimple da série 8000, use a tabela a seguir para determinar qual ação realizar de acordo com a versão de software do dispositivo executada.

| Se o dispositivo estiver em execução| Realize a seguinte ação                                    |
|--------------------------|------------------------|
| Atualização 5 ou posterior e o dispositivo offline. <br> Você verá um alerta de que a URL não está aprovada.|1. modifique as regras de firewall para incluir a URL de autenticação. Consulte [URLs de autenticação](#url-changes-for-aad-authentication).<br>2. [obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys).<br>3. [Conecte-se à interface do Windows PowerShell do dispositivo StorSimple 8000 Series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. use `Redo-DeviceRegistration` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 5 ou posterior e o dispositivo online.| Nenhuma ação é necessária.                                       |
| Atualização 4 ou anterior e o dispositivo offline. |1. modifique as regras de firewall para incluir a URL de autenticação.<br>2. [Baixe a atualização 5 por meio do servidor de catálogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [aplique a atualização 5 por meio do método de hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys).<br>5. [Conecte-se à interface do Windows PowerShell do dispositivo StorSimple 8000 Series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. use `Redo-DeviceRegistration` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 4 ou anterior e o dispositivo online. |Modifique as regras de firewall para incluir a URL de autenticação.<br> Instale a Atualização 5 por meio do portal do Azure.              |
| Aplique a redefinição de fábrica para uma versão anterior à Atualização 5.      |O portal mostra uma chave de registro baseada no AAD enquanto o dispositivo está executando o software mais antigo. Siga as etapas no cenário anterior para os casos em que o dispositivo executa a Atualização 4 ou anterior.              |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseado no AAD

A partir da Atualização 5 dos dispositivos StorSimple da série 8000, novas chaves de registro baseado no AAD são usadas. Use as chaves de registro para registrar o serviço Gerenciador de Dispositivos do StorSimple no dispositivo.

Você não poderá usar as novas chaves de registro do serviço do AAD se estiver usando um dispositivo StorSimple da série 8000 que executam a Atualização 4 ou anterior (inclui um dispositivo mais antigo que está sendo ativado agora).
Neste cenário, é necessário regenerar a chave de registro do serviço. Depois de regenerar a chave, a nova chave é usada para registrar todos os dispositivos seguintes. A chave antiga deixa de ser válida.

- A nova chave de registro do AAD expira após 3 dias.
- As chaves de registro do AAD funcionam somente com dispositivos StorSimple da série 8000 que executam a Atualização 5 ou posterior.
- As chaves de registro do AAD são mais longas do que as chaves de registro do ACS correspondentes.

Execute as etapas a seguir para gerar uma chave de registro do serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registro do serviço do AAD

1. Em **Gerenciador de Dispositivos do StorSimple**, acesse **Gerenciamento &gt;** **Chaves**. Também use a barra de pesquisa para pesquisar _Chaves_.
    
2. Clique em **Gerar chave**.

    ![Clique em regenerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave antiga deixará de funcionar.

    ![Confirmar a regeneração](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se estiver criando um Dispositivo de Nuvem StorSimple no serviço registrado para o dispositivo StorSimple da série 8000, não gere uma chave de registro enquanto a criação estiver em andamento. Aguarde conclusão da criação e, em seguida, gere a chave de registro.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como implantar um [dispositivo StorSimple da série 8000](storsimple-8000-deployment-walkthrough-u2.md).
