---
title: Criar alertas personalizados para a Versão Prévia da Central de Segurança do Azure para IoT | Microsoft Docs
description: Crie e atribua alertas de dispositivo personalizados para a Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 12559af013d49e557ba0132bef24867867745c16
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618039"
---
# <a name="quickstart-create-custom-alerts"></a>Início Rápido: Criar alertas personalizados

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usando alertas e grupos de segurança personalizados, aproveite ao máximo as informações de segurança de ponta a ponta e o conhecimento categórico do dispositivo para garantir a melhor segurança em solução de IoT. 

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados? 

Você conhece melhor seus dispositivos de IoT.

Para clientes que compreendem totalmente o comportamento esperado do seu dispositivo, a ASC (Central de Segurança do Azure) para IoT permite que se traduza esse reconhecimento em uma política de comportamento do dispositivo e alerte sobre qualquer desvio do esperado, o comportamento normal.

## <a name="security-groups"></a>Grupos de segurança

Grupos de segurança permitem que você defina grupos lógicos de dispositivos e gerencie seu estado de segurança de forma centralizada.

Esses grupos podem representar dispositivos com o hardware específico, implantados em um determinado local, ou qualquer outro grupo adequado às suas necessidades específicas de dispositivos.

Grupos de segurança são definidos por uma propriedade de marca do módulo gêmeo de segurança denominada **SecurityGroup**. Altere o valor dessa propriedade para alterar o grupo de segurança de um dispositivo.  

Por padrão, cada solução de IoT no Hub IoT tem um grupo de segurança chamado **padrão**.

Use grupos de segurança para agrupar seus dispositivos em categorias lógicas. Depois de criar os grupos, atribua-os para os alertas personalizados de sua escolha, para a solução de ponta a ponta mais eficaz. 

## <a name="customize-an-alert"></a>Personalizar um alerta

1. Abra seu Hub IoT. 
2. Clique em **Alertas personalizados** na seção **Segurança**. 
3. Escolha um grupo de segurança que você deseja aplicar a personalização. 
4. Clique em **Adicionar um alerta personalizado** 
5. Selecione um comportamento de alerta personalizado na lista suspensa. 
6. Edite as propriedades necessárias, clique em **OK**.
7. Certifique-se de clicar em **Salvar**. Sem salvar o novo alerta, o alerta é excluído na próxima vez que você fechar o Hub IoT.

 
## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

A tabela a seguir fornece um resumo de alertas disponíveis para personalização.

| Severity | NOME                                                                                                    | Fonte de dados | DESCRIÇÃO                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Baixo      | Alerta Personalizado – o número de mensagens da nuvem para o dispositivo no protocolo AMQP não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo AMQP) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para o dispositivo rejeitadas no protocolo AMQP não está no intervalo permitido | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo AMQP) que foram rejeitadas pelo dispositivo em uma janela de tempo não está no intervalo configurado permitido |
| Baixo      | Alerta Personalizado – o número de mensagens do dispositivo para a nuvem no protocolo AMQP não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens do dispositivo para a nuvem (protocolo AMQP) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta personalizado – o número de invocações de método direto não está no intervalo permitido                              | Hub IoT     | A quantidade de invocações de método direto não está no intervalo configurado permitido                                                     |
| Baixo      | Alerta personalizado – o número de uploads de arquivos não está no intervalo permitido                                       | Hub IoT     | A quantidade de uploads de arquivos em uma janela de tempo não está no intervalo configurado permitido                                                              |
| Baixo      | Alerta Personalizado – o número de mensagens da nuvem para o dispositivo no protocolo HTTP não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta Personalizado – o número de mensagens da nuvem para o dispositivo rejeitadas no protocolo HTTP não está no intervalo permitido | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo HTTP) que foram rejeitadas pelo dispositivo em uma janela de tempo não está no intervalo configurado permitido |
| Baixo      | Alerta Personalizado – o número de mensagens do dispositivo para a nuvem no protocolo HTTP não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens do dispositivo para a nuvem (protocolo HTTP) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta personalizado – o número de mensagens da nuvem para dispositivo no protocolo MQTT não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo MQTT) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta Personalizado – o número de mensagens da nuvem para o dispositivo rejeitadas no protocolo MQTT não está no intervalo permitido | Hub IoT     | A quantidade de mensagens da nuvem para o dispositivo (protocolo MQTT) que foram rejeitadas pelo dispositivo em uma janela de tempo não está no intervalo permitido |
| Baixo      | Alerta Personalizado – o número de mensagens do dispositivo para a nuvem no protocolo MQTT não está no intervalo permitido          | Hub IoT     | A quantidade de mensagens do dispositivo para a nuvem (protocolo MQTT) em uma janela de tempo não está no intervalo configurado permitido                                  |
| Baixo      | Alerta Personalizado – o número de limpezas de filas de comando não está no intervalo permitido                               | Hub IoT     | A quantidade de limpezas de filas de comando em uma janela de tempo não está no intervalo configurado permitido                                                      |
| Baixo      | Alerta Personalizado – o número de updates de gêmeos não está no intervalo permitido                                       | Hub IoT     | A quantidade de uploads de gêmeos em uma janela de tempo não está no intervalo configurado permitido                                                              |
| Baixo      | Alerta Personalizado – o número de operações não autorizadas não está no intervalo permitido                            | Hub IoT     | A quantidade de operações não autorizadas em uma janela de tempo não está no intervalo configurado permitido                                                   |
| Baixo      | Alerta Personalizado – o número de conexões ativas não está no intervalo permitido                                        | Agente       | A quantidade de conexões ativas em uma janela de tempo não está no intervalo configurado permitido                                                        |
| Baixo      | Alerta Personalizado – foi criada uma conexão de saída para um IP que não é permitido                              | Agente       | Foi criada uma conexão de saída para um IP que não é permitido                                                                                  |
| Baixo      | Alerta Personalizado – o número de logons locais com falha não está no intervalo permitido                                | Agente       | A quantidade de logons locais com falha em uma janela de tempo não está no intervalo configurado permitido                                                       |
| Baixo      | Alerta personalizado – logon de um usuário que não é permitido                                                      | Agente       | Um usuário local que não é permitido conectou-se ao dispositivo                                                                                        |
| Baixo      | Alerta Personalizado – execução de um processo que não é permitido                                               | Agente       | Um processo que não é permitido foi executado no dispositivo |          |

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implantar um agente de segurança](how-to-deploy-agent.md)
