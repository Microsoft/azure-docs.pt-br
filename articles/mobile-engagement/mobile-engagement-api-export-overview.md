---
title: "Visão geral da API de Exportação do Mobile Engagement"
description: "Saiba os conceitos básicos sobre como exportar seus dados brutos gerados por dispositivos do usuário para aproveitá-los em suas próprias ferramentas"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="mobile-engagement-export-api-overview"></a>Visão geral da API de Exportação do Mobile Engagement
## <a name="introduction"></a>Introdução
Neste documento, você aprenderá os conceitos básicos sobre como exportar seus dados brutos gerados por dispositivos do usuário para aproveitá-los em suas próprias ferramentas.

## <a name="pre-requisites"></a>Pré-requisitos
A exportação dos dados brutos do Mobile Engagement exige:

* Que a configuração de autenticação da API seja capaz de usar as APIs (confira a [configuração manual de autenticação](mobile-engagement-api-authentication-manual.md)),
* O uso das APIs REST ou do [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Uma conta de armazenamento do Azure.

> [!NOTE]
> Também recomendamos o excelente [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), pelo menos durante a fase de desenvolvimento, visto que ele fornece uma interface do usuário fácil de usar para interagir com o Armazenamento do Azure.
> 
> 

## <a name="what-can-be-exported"></a>O que pode ser exportado?
O Mobile Engagement permite que seus usuários coletem muitos tipos de dados e, portanto, tem vários tipos de exportações adequados para esses tipos de dados diferentes.
Há dois tipos essenciais de exportação:

* Instantâneo: usado normalmente para exportar dados que representam um estado e para os quais o Mobile Engagement não tem um histórico. Isso inclui Marcações (informações do aplicativo), tokens ou comentários de campanha de envio por push, por exemplo. Como consequência, esses tipos de exportação não estão relacionados a uma data.
* histórica: esse tipo de exportação é usado para dados acumulados ao longo do tempo, como Eventos ou Atividades, por exemplo.

A tabela a seguir descreve todas as possíveis exportações de maneira completa:

| Tipo de Exportação | Tipo de Dados | Descrição |
| --- | --- | --- |
| Instantâneo |Empurrar |Gera uma exportação de comentários de Campanhas de Envio por Push em uma base de deviceid/userid |
| Instantâneo |Marca |Gera uma exportação de marcações (informações do aplicativo) associadas a cada dispositivo |
| Instantâneo |Dispositivo |Gera uma exportação da maioria dos dados sobre os dispositivos, como dados técnicos (modelo, localidade, fuso horário,...), as marcações, primeira vez que foram vistos, ... |
| Instantâneo |A criptografia do token |Gera uma exportação de todos os tokens válidos |
| Histórica |Atividade |Gera uma exportação de todas as atividades para cada dispositivo em um determinado período de tempo |
| Histórica |Evento |Gera uma exportação de todas as atividades para cada dispositivo em um determinado período de tempo |
| Histórica |Trabalho |Gera uma exportação de todos os trabalhos para cada dispositivo em um determinado período de tempo |
| Histórica |Erro |Gera uma exportação de todos os erros para cada dispositivo em um determinado período de tempo |

## <a name="how-does-it-work"></a>Como ele funciona?
Exportações são tarefas de longa execução que podem produzir arquivos de dados grandes. Por esse motivo, elas não podem ser chamadas para retornarem um arquivo para download imediatamente.
Para exportar dados do Mobile Engagement, você precisará criar um **Trabalho de Exportação** por meio da API na qual você geralmente especifica:

* O tipo de exportação (instantâneo ou histórico),
* O tipo de dados,
* O **Contêiner de Armazenamento do Azure** (incluindo uma SAS válida com acesso de gravação), no qual o resultado da exportação será gravado.
* Por exemplo, o parâmetro de URL de Contêiner de Exemplo seria https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]  

Aqui está um exemplo do mundo real. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Observe que pode levar alguns minutos para que o trabalho seja iniciado. Então, ele poderá ser executado em poucos segundos, em aplicativos pequenos, ou em várias horas, em aplicativos com muitos usuários ou atividades.

Depois que o trabalho for criado, será possível verificar seu status para ver se ele ainda está em execução ou se foi concluído.

Uma vez que o trabalho for BEM-SUCEDIDO, o arquivo de dados resultante estará disponível no contêiner de armazenamento fornecido.

