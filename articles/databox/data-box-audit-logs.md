---
title: Logs de auditoria para Azure Data Box, Azure Data Box Heavy eventos | Microsoft Docs
description: Descreve os logs de auditoria completos para Data Box coletados em vários estágios de sua Azure Data Box e Azure Data Box Heavy ordem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209240"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Logs de auditoria para seu Azure Data Box e Azure Data Box Heavy

Os logs são imutáveis, registros de carimbo de data/hora de eventos discretos que ocorreram ao longo do tempo. Os logs contêm informações de diagnóstico, auditoria e segurança do seu dispositivo.  

Um pedido de Data Box ou Data Box Heavy passa pelas etapas a seguir durante a operação: pedido, configuração, cópia de dados, retorno, carregamento no Azure e verificação e eliminação de dados. Para cada uma dessas etapas, todos os eventos são auditados e registrados.

Este artigo contém informações sobre os Data Box logs de auditoria, incluindo os tipos de logs e as informações coletadas, bem como o local dos logs. 

As informações neste artigo se aplicam a ambos, Data Box e Data Box Heavy. Nas seções subsequentes, todas as referências a Data Box também se aplicam a Data Box Heavy. Os logs coletados do serviço de Data Box em execução no Azure não são abordados neste artigo. 


## <a name="about-audit-logs"></a>Sobre logs de auditoria 

No Data Box, os seguintes logs são coletados:

- **Logs do sistema** – data Box ser um dispositivo baseado no Windows, todos os eventos de hardware, software e sistema são registrados. Um conjunto desses eventos é coletado e relatado nos logs de auditoria do sistema. 

- **Segurança** -data Box ser um dispositivo baseado no Windows, todos os eventos de segurança são registrados. Um conjunto desses eventos é coletado e relatado nos logs de auditoria de segurança. 

- **Aplicativo** -esses logs são específicos somente para data box. Esses logs contêm todos os eventos gerados no dispositivo em resposta aos serviços de Data Box que estão em execução.

Cada um desses logs é discutido na seção a seguir.

### <a name="system-logs"></a>Logs do sistema

As seguintes IDs de evento de log do sistema são coletadas como logs de auditoria do sistema no seu Data Box:

|Nome do provedor de eventos     |ID do evento coletado   |Descrição do evento   |
|-------------------|----------|----------------|
|Microsoft-Windows-kernel-geral|12  |Hora UTC quando o sistema operacional foi reinicializado.   |
|                                |13  |Hora UTC quando o sistema operacional foi desligado. |
|    |                              |
|Microsoft-Windows-kernel-Power  |41  |Sistema reinicializado sem um desligamento normal.| 
|    |                              |
|Microsoft-Windows-BitLocker-driver|Todos|    |

### <a name="security-logs"></a>Logs de segurança

As seguintes IDs de evento de log de segurança são coletadas como logs de auditoria de segurança no seu Data Box:

|Nome do provedor de eventos                   |ID do evento coletado    |Descrição do evento       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |Logon bem-sucedido. |
|                                      |4625        |Falha no logon da conta. Nome de usuário desconhecido ou senha inadequada. |
|                                     

### <a name="application-logs"></a>Logs de aplicativo

As IDs de evento de log de aplicativo a seguir são coletadas como parte dos logs de auditoria de pacote no seu Data Box.     

- **Microsoft-Azure-data Box-OOBE-Auditing** -contém os eventos que ocorrem na interface do usuário local. 
- **Microsoft-Azure-data Box-reprovision-Audit** -contém eventos relacionados ao reprovisionamento do dispositivo data box. O reprovisionamento do Data Box ocorre quando o dispositivo é redefinido por meio da interface do usuário local. Você escolhe essa opção quando deseja apagar os dados copiados removendo os compartilhamentos existentes e recriando os compartilhamentos como parte do reprovisionamento ou da redefinição do dispositivo.
- **Microsoft-Azure-data Box-HcsMgmt-Audit** -contém eventos relacionados somente à etapa **preparar para enviar** antes que o dispositivo seja enviado de volta para o datacenter do Azure. 
- **Microsoft-Azure-data Box-IfxAudit** -contém as mensagens registradas por diferentes entidades do produto sobre os trabalhos, logs que indicam mais informações sobre o que está acontecendo em alguns dos fluxos.

Aqui está uma tabela que resume os vários provedores de eventos e as IDs de evento correspondentes que são coletadas em cada caso.

|Nome do provedor de eventos    |ID do evento    | Observações |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-data Box-OOBE-Auditing |4624        |Logon bem-sucedido.|
|                                      |4625        |Falha no logon da conta. Nome de usuário desconhecido ou senha inadequada.|
|                                     |4634        |Evento de logoff.|
|                                   |  | |
|Microsoft-Azure-data Box-reprovisionar-auditoria    |65001       |Evento de reprovisionamento bem-sucedido.|
|                                                  |65002       |Falha ao reprovisionar o evento.|
|                                                  |                 |         |
|Microsoft-Azure-data Box-HcsMgmt-Audit        |65003       |Preparação para o envio evento de estado não foi iniciado, em andamento, com falha, cancelado, com êxito, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-data Box-IfxAudit    |Todos |Todos os eventos são registrados com a API do log de auditoria no código |

Aqui está um exemplo do log de auditoria do IFX (Instrumentation Framework):

|     Tarefa/trabalho/API                              |     Eventos registrados                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Limpeza                                   |     Os eventos relacionados ao início, à conclusão ou à falha de um trabalho de limpeza são registrados. |                                              
|     Preparar o dispositivo para a remessa do cliente    |     Os eventos relacionados ao início, à conclusão ou à falha do trabalho para preparar o dispositivo para envio são registrados. |
|     Provisionar                                 |     Os eventos relacionados ao início, à conclusão ou à falha de um trabalho de provisionamento de dispositivos são registrados.|
|     Trabalho do pacote de auditoria                       |     Os eventos relacionados ao início, à conclusão ou à falha de um trabalho de pacote de auditoria que cria cadeias de logs de custódia são registrados.|
|     Substituição de disco                          |     A falha na substituição do disco é registrada.|
|     Habilitar ou desabilitar o PowerShell remoto     |     Os eventos relacionados à habilitação ou desabilitação do PowerShell remoto no dispositivo são registrados. |
|     Obter detalhes da fase de instalação               |     Os eventos relacionados à instalação do software no dispositivo em fases são registrados no datacenter do Azure.|
|     Desbloquear ou bloquear o volume do BitLocker           |     Os eventos são registrados para indicar o status do BitLocker do volume de *basevolume* e *hcsdata* .|
|     Limpar disco                              |     Os eventos relacionados à falha de discos físicos que não puderam ser apagados, e os eventos quando todos os discos físicos no dispositivo são apagados com êxito, são registrados em log. |
|     Habilitar ou desabilitar usuário local               |     Os eventos relacionados à habilitação ou desabilitação de contas de usuário local para StorSimpleAdmin e PodSupportAdminUser são registrados.| 
|     Redefinição de senha                          |     Os eventos relacionados à redefinição de senha bem-sucedida ou com falha para o usuário StorSimpleAdmin local são registrados. |


Além dos logs de auditoria do IFX, os logs de auditoria da cadeia de custódia também são coletados para Data Box. Esses logs não podem ser exibidos em tempo real, mas somente depois que o trabalho é concluído e os dados são apagados dos discos Data Box. Esses logs contêm um subconjunto das informações contidas nos logs de auditoria do IFX.

Para obter mais informações sobre a cadeia de logs de auditoria de custódia, consulte [obter cadeia de logs de custódia após a eliminação de dados](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Esses logs são armazenados no Azure e não podem ser acessados diretamente. Se você precisar acessar esses logs, registre um tíquete de suporte. Para obter mais informações, consulte [Contate o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md). 

Depois que o tíquete de suporte for arquivado, a Microsoft baixará e fornecerá acesso a esses logs.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas em seu data box e data Box Heavy](data-box-troubleshoot.md).
