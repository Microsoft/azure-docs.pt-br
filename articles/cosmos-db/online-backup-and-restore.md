---
title: Backup online e restauração de dados sob demanda no Azure Cosmos DB.
description: Este artigo descreve como o backup automático, a restauração de dados sob demanda funciona. Ele também explica a diferença entre os modos de backup contínuos e periódicos.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525408"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup on-line e restauração de dados sob demanda no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade das operações do banco de dados. Todos os backups são armazenados separadamente em um serviço de armazenamento. Os backups automáticos são úteis em cenários quando você acidentalmente exclui ou atualiza sua conta, banco de dados ou contêiner do Azure Cosmos e, posteriormente, exige a recuperação de dados. Há dois modos de backup:

* **Modo de backup periódico** – esse modo é o modo de backup padrão para todas as contas existentes. Nesse modo, o backup é feito em um intervalo periódico e os dados são restaurados por meio da criação de uma solicitação com a equipe de suporte. Nesse modo, você configura um intervalo de backup e uma retenção para sua conta. O período máximo de retenção se estende a um mês. O intervalo mínimo de backup pode ser de uma hora.  Para saber mais, consulte o artigo [modo de backup periódico](configure-periodic-backup-restore.md) .

* **Modo de backup contínuo** (atualmente em visualização pública) – você escolhe esse modo ao criar a conta de Azure Cosmos DB. Esse modo permite que você faça a restauração em qualquer ponto de tempo nos últimos 30 dias. Para saber mais, confira a [introdução ao modo de backup contínuo](continuous-backup-restore-introduction.md), configurar o backup contínuo com os artigos [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)e [Resource Manager](continuous-backup-restore-template.md) .

  > [!NOTE]
  > Se você configurar uma nova conta com backup contínuo, poderá fazer a restauração por autoatendimento via portal do Azure, PowerShell ou CLI. Se sua conta estiver configurada no modo contínuo, você não poderá alternar de volta para o modo periódico. Atualmente, as contas existentes com o modo de backup periódico não podem ser alteradas para o modo contínuo.  

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender a configurar e gerenciar os modos de backup periódicos e contínuos para sua conta:

* [Configure e gerencie a política de backup periódico](configure-periodic-backup-restore.md) .
* O que é o modo de [backup contínuo](continuous-backup-restore-introduction.md) ?
* Configure e gerencie o backup contínuo usando [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
