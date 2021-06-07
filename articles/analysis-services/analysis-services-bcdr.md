---
title: Alta disponibilidade do Azure Analysis Services | Microsoft Docs
description: Este artigo descreve como Azure Analysis Services fornece alta disponibilidade durante a interrupção do serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506738"
---
# <a name="analysis-services-high-availability"></a>Alta disponibilidade do Analysis Services

Este artigo descreve como garantir alta disponibilidade para servidores do Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantindo alta disponibilidade durante uma interrupção do serviço

Embora seja raro, um data center do Azure pode sofrer uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios que pode durar alguns minutos ou até mesmo horas. A alta disponibilidade geralmente é obtida com redundância do servidor. Com o Azure Analysis Services, você pode obter redundância criando servidores secundários adicionais em uma ou mais regiões. Ao criar servidores redundantes, para garantir que os dados e metadados nesses servidores estejam em sincronia com o servidor em uma região que ficou offline, você pode:

* Implantar modelos em servidores redundantes em outras regiões. Esse método requer processamento de dados no servidor primário e servidores redundantes em paralelo, garantindo que todos os servidores estejam em sincronia.

* [Faça backup](analysis-services-backup.md) de bancos de dados do seu servidor primário e restaure em servidores redundantes. Por exemplo, você pode automatizar backups noturnos no Armazenamento do Azure e restaurar para outros servidores redundantes em outras regiões. 

Em ambos os casos, se o servidor primário sofre uma interrupção, você deve alterar as cadeias de conexão em clientes de relatório para se conectar ao servidor em um datacenter regional diferente. Essa alteração deve ser considerada um último recurso e apenas caso ocorra uma interrupção catastrófica de data center regional. É mais provável que, após uma interrupção, o data center que hospeda o servidor primário fique online novamente antes de você poder atualizar conexões em todos os clientes. 

Para evitar a necessidade de alterar as cadeias de conexão nos clientes com relatórios, você pode criar um [alias](analysis-services-server-alias.md) do servidor para o servidor principal. Se o servidor primário falhar, você poderá alterar o alias para apontar a um servidor redundante em outra região. É possível automatizar alias para o nome do servidor, codificando uma verificação de integridade do ponto de extremidade no servidor primário. Se a verificação de integridade falhar, o mesmo ponto de extremidade poderá direcionar para um servidor redundante em outra região. 

## <a name="related-information"></a>Informações relacionadas

[Backup e restauração](analysis-services-backup.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md)   
[Nome de servidor de alias](analysis-services-server-alias.md) 

