---
title: Visão geral do modo de dispositivo compartilhado
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o modo de dispositivo compartilhado para habilitar o compartilhamento de dispositivos para seus trabalhos do frente.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf8869002fb3e0170331709af3da5b971a098740
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612391"
---
# <a name="overview-of-shared-device-mode"></a>Visão geral do modo de dispositivo compartilhado

O modo de dispositivo compartilhado é um recurso do Azure Active Directory que permite que você crie aplicativos que dão suporte a trabalhadores frente e habilite o modo de dispositivo compartilhado nos dispositivos implantados neles.

>[!IMPORTANT]
> Modo de dispositivo compartilhado para iOS [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>O que são operadores de frente?

Os frente Workers são funcionários de varejo, agentes de manutenção e campo, pessoal médico e outros usuários que não ficam na frente de um computador ou usam o email corporativo para colaboração. As seções a seguir apresentam os aspectos e os desafios de dar suporte a trabalhadores do frente, seguidos por uma introdução aos recursos fornecidos pela Microsoft que permitem que seu aplicativo seja usado pelos trabalhos de frente de uma organização.

### <a name="challenges-of-supporting-frontline-workers"></a>Desafios de dar suporte a trabalhadores do frente

A habilitação de fluxos de trabalho do frente Worker inclui desafios que geralmente não são apresentados por operadores de informações típicos. Esses desafios podem incluir alta taxa de rotatividade e menos familiaridade com as ferramentas de produtividade principais de uma organização. Para capacitar seus trabalhos frentes, as organizações estão adotando estratégias diferentes. Alguns estão adotando uma estratégia BYOD (Traga seu próprio dispositivo) na qual seus funcionários usam aplicativos de negócios em seu telefone pessoal, enquanto outros fornecem aos seus funcionários dispositivos compartilhados, como tablets iPads ou Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Suporte a vários usuários em dispositivos criados para um usuário

Como os dispositivos móveis que executam o iOS ou Android foram projetados para usuários únicos, a maioria dos aplicativos otimiza sua experiência para uso por um único usuário. Parte dessa experiência otimizada significa habilitar o logon único entre aplicativos e manter os usuários conectados em seu dispositivo. Quando um usuário remove sua conta de um aplicativo, o aplicativo normalmente não considera um evento relacionado à segurança. Muitos aplicativos até mesmo mantêm as credenciais de um usuário para entrada rápida. Você pode até mesmo ter feito isso quando tiver excluído um aplicativo do seu dispositivo móvel e, em seguida, reinstalá-lo, apenas para descobrir que você ainda está conectado.

### <a name="global-sign-in-and-sign-out-sso"></a>Entrada global e saída (SSO)

Para permitir que os funcionários de uma organização usem seus aplicativos em um pool de dispositivos compartilhados por esses funcionários, os desenvolvedores precisam habilitar a experiência oposta. Os funcionários devem ser capazes de escolher um dispositivo do pool e executar um único gesto para "torná-lo" para a duração de sua mudança. No final de seu turno, eles devem ser capazes de executar outro gesto para sair globalmente no dispositivo, com todas as suas informações pessoais e da empresa removidas para que possam devolvê-las ao pool de dispositivos. Além disso, se um funcionário esquecer de sair, o dispositivo deverá ser desconectado automaticamente no final do turno e/ou após um período de inatividade.

Azure Active Directory habilita esses cenários com um recurso chamado de **modo de dispositivo compartilhado**.

## <a name="introducing-shared-device-mode"></a>Introdução ao modo de dispositivo compartilhado

Como mencionado, o modo de dispositivo compartilhado é um recurso do Azure Active Directory que permite que você:

* Criar aplicativos que dão suporte a trabalhos do frente
* Implantar dispositivos em frente Workers e ativar o modo de dispositivo compartilhado

### <a name="build-applications-that-support-frontline-workers"></a>Criar aplicativos que dão suporte a trabalhos do frente

Você pode dar suporte a trabalhadores do frente em seus aplicativos usando a biblioteca de autenticação da Microsoft (MSAL) e o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para habilitar um estado do dispositivo chamado *modo de dispositivo compartilhado*. Quando um dispositivo estiver no modo de dispositivo compartilhado, a Microsoft fornecerá ao seu aplicativo informações para permitir que ele modifique seu comportamento com base no estado do usuário no dispositivo, protegendo os dados do usuário.

Os recursos com suporte são:

* **Conecte-se a um dispositivo de usuário em todo o** aplicativo com suporte.
* **Desconecte um dispositivo de usuário em todo o** aplicativo com suporte.
* **Consulte o estado do dispositivo** para determinar se seu aplicativo está em um dispositivo que está no modo de dispositivo compartilhado.
* **Consulte o estado do dispositivo do usuário** no dispositivo para determinar se algo foi alterado desde a última vez em que seu aplicativo foi usado.

O suporte ao modo de dispositivo compartilhado deve ser considerado um aprimoramento de segurança e uma atualização de recursos para seu aplicativo e pode ajudar a aumentar sua adoção em ambientes altamente regulamentados, como saúde e finanças.

Os usuários dependem de você para garantir que seus dados não sejam vazados para outro usuário. O modo de compartilhamento de dispositivo fornece sinais úteis para indicar ao aplicativo que uma alteração que você deve gerenciar ocorreu. Seu aplicativo é responsável por verificar o estado do usuário no dispositivo toda vez que o aplicativo é usado, limpando os dados do usuário anterior. Isso inclui se ele é recarregado do plano de fundo em várias tarefas. Em uma alteração de usuário, você deve garantir que os dados do usuário anterior sejam apagados e que todos os dados armazenados em cache que estão sendo exibidos em seu aplicativo sejam removidos. Recomendamos que você sempre execute um processo de revisão de segurança completo depois de adicionar o recurso de modo de dispositivo compartilhado ao seu aplicativo.

Para obter detalhes sobre como modificar seus aplicativos para dar suporte ao modo de dispositivo compartilhado, consulte a seção [próximas etapas](#next-steps) no final deste artigo.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Implantar dispositivos em frente Workers e ativar o modo de dispositivo compartilhado

Depois que os aplicativos suportarem o modo de dispositivo compartilhado e incluírem os dados necessários e as alterações de segurança, você poderá anuncia-los como sendo utilizáveis por frente Workers.

Os administradores de dispositivos de uma organização podem implantar seus dispositivos e seus aplicativos em seus armazenamentos e locais de trabalho por meio de uma solução de MDM (gerenciamento de dispositivo móvel), como Microsoft Intune. Parte do processo de provisionamento é marcar o dispositivo como um *dispositivo compartilhado*. Os administradores configuram o modo de dispositivo compartilhado implantando o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e definindo o modo de dispositivo compartilhado por meio de parâmetros de configuração. Depois de executar essas etapas, todos os aplicativos que dão suporte ao modo de dispositivo compartilhado usarão o Microsoft Authenticator aplicativo para gerenciar seu estado de usuário e fornecer recursos de segurança para o dispositivo e a organização.

## <a name="next-steps"></a>Próximas etapas

Damos suporte a plataformas iOS e Android para o modo de dispositivo compartilhado. Examine a documentação abaixo para que sua plataforma comece a dar suporte a trabalhadores do frente em seus aplicativos.

* [Suporte ao modo de dispositivo compartilhado para iOS](msal-ios-shared-devices.md)
* [Suporte ao modo de dispositivo compartilhado para Android](msal-android-shared-devices.md)
