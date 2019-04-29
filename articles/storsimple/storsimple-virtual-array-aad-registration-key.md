---
title: Nova autenticação para Matrizes Virtuais StorSimple | Microsoft Docs
description: Explica como usar a autenticação baseada no AAD para o serviço, gerar uma nova chave de registro e realizar o registro manual dos dispositivos.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387602"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Usar a nova autenticação para o StorSimple

## <a name="overview"></a>Visão geral

O serviço do StorSimple Device Manager é executado no Microsoft Azure e conecta-se a vários StorSimple Virtual Arrays. Até o momento, o serviço Gerenciador de Dispositivos do StorSimple usou um ACS (serviço de Controle de Acesso) para autenticar o serviço no dispositivo StorSimple. O mecanismo do ACS será preterido em breve e substituído por uma autenticação do AAD (Azure Active Directory).

As informações contidas neste artigo aplicam-se somente a Matrizes Virtuais StorSimple da Série 1200. Este artigo descreve os detalhes da autenticação do AAD e a nova chave de registro do serviço associada, bem como modificações nas regras de firewall aplicáveis aos dispositivos StorSimple.

A autenticação do AAD ocorre em Matrizes Virtuais StorSimple (modelo 1200) que executam a Atualização 1 ou posterior.

Devido à introdução da autenticação do AAD, ocorrem alterações em:

- Padrões de URL para regras de firewall.
- Chave de registro do serviço.

Essas alterações são abordadas em detalhes nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para a autenticação do AAD

Para garantir que o serviço use a autenticação baseada no AAD, todos os usuários precisam incluir as novas URLs de autenticação em suas regras de firewall.

Se está usando uma Matriz Virtual StorSimple, verifique se a seguinte URL está incluída nas regras de firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos EUA |Serviço de autenticação do AAD      |

Para obter uma lista completa de padrões de URL para Matrizes Virtuais StorSimple, acesse [Padrões de URL para regras de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se a URL de autenticação não estiver incluída nas regras de firewall além da data de reprovação, os usuários verão um alerta crítico informando que o dispositivo StorSimple não pôde ser autenticado no serviço. O serviço não poderá se comunicar com o dispositivo. Se os usuários virem esse alerta, será necessário incluir a nova URL de autenticação. Para obter mais informações sobre o alerta, acesse [Usar alertas para monitorar o dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Alterações de versão e autenticação de dispositivo

Se estiver usando uma Matriz Virtual StorSimple, use a tabela a seguir para determinar qual ação você precisa realizar de acordo com a versão de software do dispositivo executada.

| Se o dispositivo estiver em execução  | Realize a seguinte ação                                    |
|----------------------------|--------------------------------------------------------------|
| Atualização 1.0 ou posterior e está offline. <br> Um alerta é exibido informando que a URL não está na lista de permissões.| 1. Modifique as regras de firewall para incluir a URL de autenticação. Consulte [URLs de autenticação](#url-changes-for-aad-authentication). <br> 2. [Obter a chave de registro do AAD do serviço](#aad-based-registration-keys). <br> 3. Execute as etapas 1 a 5 para [conectar-se à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Use o cmdlet `Invoke-HcsReRegister` para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 1.0 ou posterior e o dispositivo online.| Nenhuma ação é necessária.                                       |
| Atualização 0.6 ou anterior e o dispositivo offline. | 1. [Baixe a Atualização 1.0 por meio do servidor de catálogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Aplique a Atualização 1.0 por meio da interface do usuário da Web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Obter a chave de registro do AAD do serviço](#aad-based-registration-keys). <br>4. Execute as etapas 1 a 5 para [conectar-se à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Use o cmdlet `Invoke-HcsReRegister` para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 0.6 ou anterior e o dispositivo online | Modifique as regras de firewall para incluir a URL de autenticação.<br> Instale a Atualização 1.0 por meio do portal do Azure. |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseado no AAD

A partir da Atualização 1.0 das Matrizes Virtuais StorSimple, novas chaves de registro baseado no AAD são usadas. Use as chaves de registro para registrar o serviço Gerenciador de Dispositivos do StorSimple no dispositivo.

Você não poderá usar as novas chaves de registro do serviço do AAD se estiver usando Matrizes Virtuais StorSimple que executam a Atualização 0.6 ou anterior. É necessário regenerar a chave de registro do serviço. Depois de regenerar a chave, a nova chave é usada para registrar todos os dispositivos seguintes. A chave antiga deixa de ser válida.

- A nova chave de registro do AAD expira após 3 dias.
- As chaves de registro do AAD funcionam somente com matrizes virtuais StorSimple da série 1200 que executam a Atualização 1 ou posterior. A chave de registro do AAD de um dispositivo StorSimple da série 8000 não funcionará.
- As chaves de registro do AAD são mais longas do que as chaves de registro do ACS correspondentes.

Execute as etapas a seguir para gerar uma chave de registro do serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registro do serviço do AAD

1. Em **Gerenciador de Dispositivos do StorSimple**, acesse **Gerenciamento &gt;** **Chaves**.
    
    ![Acesse Chaves](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Clique em **Gerar chave**.

    ![Clique em regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave anterior deixará de funcionar.

    ![Confirmar a regeneração](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como implantar uma [Matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
