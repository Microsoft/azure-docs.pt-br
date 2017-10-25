---
title: "Limitações no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Descreve as limitações de visualização no Banco de Dados para MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/8/2017
ms.openlocfilehash: dc5ad012398b7d07886cd22c20975e61f820d7e0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limitações no Banco de Dados do Azure para MySQL (Visualização)
O Banco de Dados do Azure para o serviço do MySQL está em visualização pública. As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="service-tier-maximums"></a>Limites máximos da camada de serviço
O Banco de Dados do Azure para MySQL tem vários níveis de serviço que podem ser escolhidos durante a criação de um servidor. Para saber mais, confira [Entenda o que está disponível em cada camada de serviço](concepts-service-tiers.md).  

Há um número máximo de conexões, unidades de computação e armazenamento em cada camada de serviço durante a versão prévia, conforme a seguir: 

|                            |                   |
| :------------------------- | :---------------- |
| **Conexões máximas**        |                   |
| 50 unidades de computação básica     | 50 conexões    |
| 100 unidades de computação básica    | 100 conexões   |
| 100 unidades de computação standard | 200 conexões   |
| 200 unidades de computação standard | 400 conexões   |
| 400 unidades de computação standard | 400 conexões   |
| 800 unidades de computação standard | 1600 conexões  |
| **Unidades de computação máxima**      |                   |
| Camada de serviço Básica         | 100 Unidades de computação |
| Camada de serviço Standard      | 800 Unidades de computação |
| **Armazenamento Máximo**            |                   |
| Camada de serviço Básica         | 1 TB              |
| Camada de serviço Standard      | 1 TB              |

Quando um número excessivo de conexões for atingido, você receberá o seguinte erro:
> ERRO 1040 (08004): número excessivo de conexões

## <a name="preview-functional-limitations"></a>Limitações funcionais da versão prévia

### <a name="scale-operations"></a>Operações de dimensionamento
- Não há suporte para o dimensionamento dinâmico de servidores por meio de camadas de serviço. Ou seja, alternando entre as camadas de serviço Básico e Standard.
- Não há suporte para o aumento sob demanda dinâmico de armazenamento no servidor criado previamente.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

### <a name="point-in-time-restore"></a>Restauração pontual
- Não é permitido restaurar para a camada de serviço diferente e/ou Unidades de computação e Tamanho do armazenamento.
- Não há suporte para restaurar um servidor eliminado.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-service-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
