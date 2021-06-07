---
title: Implantando o SQL do Azure no Edge em um parque eólico da Contoso
description: Neste tutorial, você usará o SQL do Azure no Edge para detectar a ativação das turbinas em um parque eólico da Contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723466"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Usando o SQL do Azure no Edge para criar recursos renováveis mais inteligentes

Esta demonstração do SQL do Azure no Edge é baseada na Contoso Renewable Energy, um parque eólico que usa o banco de dados SQL no Edge para processamento de dados integrado ao gerador. 

Esta demonstração descreve a resolução de um alerta gerado devido à detecção de turbulência causada pelo vento no dispositivo. Você treinará um modelo e o implantará na Banco de dados SQL no Edge que corrigirá a ativação pelo vento detectada e, por fim, otimizará a saída de energia.

SQL do Azure no Edge – vídeo de demonstração sobre energia renovável no Canal 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Configurando a demonstração no computador local
O Git será usado para copiar todos os arquivos da demonstração para o computador local. 

1. Instale o GIT [aqui](https://git-scm.com/download).
2. Abra um prompt de comando e navegue até uma pasta na qual o repositório deve ser baixado. 
3. Execute o comando https://github.com/microsoft/sql-server-samples.git.
4. Navegue até **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** na localização em que o repositório foi clonado.
5. Siga as instruções fornecidas em README.md para configurar o ambiente de demonstração e executar a demonstração.