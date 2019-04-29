---
title: Criar um aplicativo Android nos Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Docs
description: Siga este tutorial para começar a usar back-ends de aplicativos móveis do Azure para desenvolvimento do Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 805868617fe7161159c72ba53ac0c94247722ac9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113435"
---
# <a name="create-an-android-app"></a>Criar um aplicativo Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android móvel usando um back-end de aplicativo móvel do Azure.  Você criará um novo back-end do aplicativo móvel e um aplicativo Android simples com *Lista de tarefas pendentes* que armazena dados de aplicativo no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Android sobre como usar o recurso de Aplicativos Móveis no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* [Ferramentas para desenvolvedores do Android](https://developer.android.com/sdk/index.html), que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.
* O SDK para Android do Azure Mobile, que é automaticamente referenciado como parte do projeto de início rápido baixado.
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end de aplicativo móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Baixar e executar o aplicativo Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
