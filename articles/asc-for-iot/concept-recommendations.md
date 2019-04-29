---
title: Entender a Central de segurança do Azure para as recomendações de segurança de IoT Preview | Microsoft Docs
description: Saiba mais sobre o conceito de recomendações de segurança e como eles são usados na Central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 3364ff6bcf824e5f7d925fdb6d0826d5c3c32369
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359146"
---
# <a name="security-recommendations"></a>Recomendações de segurança

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Central de segurança (ASC) do Azure para IoT examina seus dispositivos IoT e os recursos do Azure e fornece recomendações de segurança para reduzir a superfície de ataque. Recomendações de segurança são acionáveis e ter como objetivo ajudar os clientes em conformidade com práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações que podem ser disparados em seu IoT Hub e/ou dispositivos IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

Recomendações do dispositivo fornecem ideias e sugestões para melhorar a postura de segurança de dispositivo. 

| Severity | NOME                                                      | Fonte de dados | DESCRIÇÃO                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Média   | Abrir portas no dispositivo                                      | Agente       | Foi encontrado um ponto de extremidade de escuta no dispositivo                                                                                                                                                          |
| Média   | Política de firewall permissivo encontrada em uma das cadeias de. | Agente       | Permitido encontrada (entrada/saída) de diretiva de firewall. Política de firewall deve negar todo o tráfego por padrão e definem regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Média   | Regra de firewall permissivas na cadeia de entrada foi encontrada     | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma ampla variedade de portas ou endereços IP.                                                                                    |
| Média   | Regra de firewall permissivas na cadeia de saída foi encontrada    | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma ampla variedade de portas ou endereços IP.                                                                                   |
| Média   | Falha na validação de linha de base do sistema de operação           | Agente       | Dispositivo não está em conformidade com [benchmarks CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

Recomendações operacionais fornecem ideias e sugestões para melhorar a configuração do agente de segurança.

| Severity | NOME                                    | Fonte de dados | DESCRIÇÃO                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixo      | Agent envia mensagens inutilizadas          | Agente       | 10% ou mais das mensagens de segurança eram menores que 4kb durante as últimas 24 horas.  |
| Baixo      | Configuração de gêmeo de segurança não ideal | Agente       | Configuração de gêmeo de segurança não é ideal.                                        |
| Baixo      | Conflito de configuração de gêmeo de segurança    | Agente       | Conflitos foram identificados na configuração de gêmeo de segurança.                           |


## <a name="recommendations-for-iot-hub"></a>Recomendações para o Hub IoT

Os alertas de recomendação fornecem informações e sugestões de ações para melhorar a postura de segurança do seu ambiente.  

| Severity | NOME                                                     | Fonte de dados | DESCRIÇÃO                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alto     | Credenciais de autenticação idêntica usadas por vários dispositivos | Hub IoT     | As credenciais de autenticação do IoT Hub são usadas por vários dispositivos. Isso pode indicar um dispositivo ilegítimos representando um dispositivo legítimos. Uso da credencial duplicados aumenta o risco de representação do dispositivo por um ator mal-intencionado. |
| Média   | Política de filtro IP padrão deve negar                  | Hub IoT     | Configuração de filtro IP deve ter regras definidas para o tráfego permitido e deve, por padrão, negam o tráfego de todos os outros por padrão.                                                                                                     |
| Média   | Regra de filtro IP inclui grandes intervalos IP                   | Hub IoT     | Um intervalo IP de origem permitir IP filtro regra é muito grande. Regras excessivamente permissivas podem expor seu hub IoT para atores mal-intencionados.                                                                                       |
| Baixo      | Habilitar os logs de diagnóstico no Hub IoT                       | Hub IoT     | Habilite os logs e retenha-os por até um ano. Mantendo logs permite que você recrie trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou sua rede está comprometido.                                       |
|