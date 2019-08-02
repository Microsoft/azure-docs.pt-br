---
title: Proteção Avançada contra Ameaças do Armazenamento do Azure
description: Configure a Proteção avançada contra ameaças do Armazenamento do Azure para detectar anomalias na atividade da conta e notificá-lo de eventuais tentativas prejudiciais de acessar sua conta.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 5d0b41344f4a77f3205799be7c30526e2a150523
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479908"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Proteção Avançada contra Ameaças do Armazenamento do Azure

A Proteção Avançada contra Ameaças do Armazenamento do Azure oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem precisar ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança. 

Os alertas de segurança são disparados quando ocorrem anomalias na atividade.  Esses alertas de segurança são integrados à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças.

> [!NOTE]
> * A proteção avançada contra ameaças para o armazenamento do Azure está disponível no momento apenas para o armazenamento de BLOBs.
> * Para obter detalhes de preços, incluindo uma avaliação gratuita de 30 dias, consulte a [página de preços da central de segurança do Azure]( https://azure.microsoft.com/pricing/details/security-center/).
> * O recurso ATP para armazenamento do Azure não está disponível atualmente nas regiões Azure governamental e soberanas Cloud.

A proteção avançada contra ameaças para o armazenamento do Azure ingere logs de diagnóstico de solicitações de leitura, gravação e exclusão para o armazenamento de BLOB para detecção de ameaças. Para investigar os alertas da proteção avançada contra ameaças, você pode exibir a atividade de armazenamento relacionada usando o log de Análise de Armazenamento. Para obter mais informações, consulte como [Configurar o log de análise de armazenamento](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças 

### <a name="using-the-portal"></a>Usando o portal

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Navegue até a página de configuração da conta de Armazenamento do Azure que você deseja proteger. Na página **Configurações**, selecione **Proteção avançada contra ameaças**.

3. Na folha de configuração **Proteção avançada contra ameaças**
    * **ATIVAR** *Proteção avançada* contra ameaças
    * Clique em **Salvar** para salvar a política de Proteção avançada contra ameaças nova ou atualizada. (Os preços na imagem são apenas para fins de exemplo.)

![Ativar a proteção avançada contra ameaças do Armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Como usar a Central de Segurança do Azure

Quando você assina a camada Standard na central de segurança do Azure, a proteção avançada contra ameaças é configurada automaticamente em todas as suas contas de armazenamento. Você pode habilitar ou desabilitar a proteção avançada contra ameaças para suas contas de armazenamento em uma assinatura específica da seguinte maneira:

1. Inicie a **central de segurança do Azure** no [portal do Azure]([https://portal.azure.com).
1. No menu principal, clique em **preços & configurações**.
1. Clique na assinatura que você deseja habilitar ou desabilitar a proteção contra ameaças para suas contas de armazenamento.

    ![Selecionar assinatura](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique em **Tipo de preço**.
1. Na seção **selecionar camada de preços por tipo de recurso** , na **linha contas de armazenamento** , clique em **habilitado** ou **desabilitado**.

    ![Habilitar ATP na central de segurança](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Salvar**.

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

Use um modelo de Azure Resource Manager para implantar uma conta de armazenamento do Azure com a proteção avançada contra ameaças habilitada. Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Usando Azure Policy

Use um Azure Policy para habilitar a proteção avançada contra ameaças em contas de armazenamento em uma assinatura ou grupo de recursos específico.

1. Inicie a página de **definições de política** do Azure.

1. Procure a política **implantar proteção avançada contra ameaças em contas de armazenamento** .

     ![Política de pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Selecione uma assinatura do Azure ou um grupo de recursos.

    ![Selecionar assinatura ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a política.

    ![Página Definições de política](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Usando a API REST
Use comandos da API REST para criar, atualizar ou obter a configuração de proteção avançada contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada contra ameaças – criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças-obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Use os seguintes cmdlets do PowerShell:

  * [Habilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Obtenha proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Desabilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* o nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As possíveis causas 
* As etapas de investigação
* As etapas de correção


O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

É possível examinar e gerenciar seus alertas de segurança atuais no [bloco Alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) da Central de Segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Esses eventos podem disparar os alertas a seguir:

### <a name="anomalous-access-pattern-alerts"></a>Alertas de padrão de acesso anormal

* **Acesso de local incomum**: Esse alerta é disparado quando alguém acessa uma conta de armazenamento a partir de uma localização geográfica incomum.
Possíveis causas:
   * Um invasor acessou sua conta de armazenamento
   * Um usuário legítimo acessou sua conta de armazenamento a partir de um novo local
 
* **Anomalia do aplicativo**: Esse alerta indica que um aplicativo incomum acessou essa conta de armazenamento. Possíveis causas:
   * Um invasor acessou sua conta de armazenamento usando um novo aplicativo.
   * Um usuário legítimo usou um novo aplicativo/navegador para acessar sua conta de armazenamento.

* **Acesso anônimo**: Esse alerta indica que essa conta foi acessada anonimamente (ou seja, sem nenhuma autenticação), o que é inesperado em comparação com o padrão de acesso recente nessa conta.
Possíveis causas:
   * Um invasor explorou o acesso de leitura público a um contêiner.
   * Um usuário ou aplicativo legítimo usou acesso de leitura público a um contêiner.

* **Anomalia de Tor**: Esse alerta indica que essa conta foi acessada com êxito de um endereço IP que é conhecido como um nó de saída ativo de Tor (um proxy de anonimato). A severidade desse alerta considera o tipo de autenticação usado (se houver) e se este é o primeiro caso desse acesso.
Possíveis causas:
   * Um invasor acessou sua conta de armazenamento usando o Tor.
   * Um usuário legítimo acessou sua conta de armazenamento usando o Tor.


### <a name="anomalous-extractupload-alerts"></a>Alertas de extração/carregamento anormais

* **Vazamento de dados**: Esse alerta indica que uma quantidade incomum de dados muito grande foi extraída em comparação com a atividade recente neste contêiner de armazenamento. Possíveis causas:
   * Um invasor extraiu uma grande quantidade de dados de um contêiner. (Por exemplo: vazamento/violação de dados, transferência não autorizada de dados)
   * Um usuário ou aplicativo legítimo extraiu uma quantidade incomum de dados de um contêiner. (Por exemplo: atividade de manutenção)

* **Exclusão inesperada**: Esse alerta indica que uma ou mais operações de exclusão inesperadas ocorreram em uma conta de armazenamento, em comparação com a atividade recente nessa conta. Possíveis causas:
   * Um invasor excluiu dados de sua conta de armazenamento.
   * Um usuário legítimo executou uma exclusão incomum.

* **Carregar o pacote de serviço de nuvem do Azure**: Esse alerta indica que um pacote de serviço de nuvem do Azure (arquivo. cspkg) foi carregado em uma conta de armazenamento de maneira incomum, em comparação com a atividade recente nessa conta. Possíveis causas: 
   * Um invasor está se preparando para implantar código mal-intencionado de sua conta de armazenamento em um serviço de nuvem do Azure.
   * Um usuário legítimo está se preparando para uma implantação de serviço legítima.

### <a name="suspicious-storage-activities-alerts"></a>Alertas de atividades de armazenamento suspeitas

* **Alteração de permissão de acesso**: Esse alerta indica que as permissões de acesso deste contêiner de armazenamento foram alteradas de maneira incomum. Possíveis causas: 
   * Um invasor alterou as permissões de contêiner para enfraquecer sua segurança.
   * Um usuário legítimo alterou as permissões de contêiner.

* **Inspeção de acesso**: Esse alerta indica que as permissões de acesso de uma conta de armazenamento foram inspecionadas de forma incomum, em comparação com a atividade recente nessa conta. Possíveis causas: 
   * Um invasor executou o reconhecimento para um ataque futuro.
   * Um usuário legítimo realizou a manutenção na conta de armazenamento.

* **Exploração de dados**: Esse alerta indica que os BLOBs ou contêineres em uma conta de armazenamento foram enumerados de forma incomum, em comparação com a atividade recente nessa conta. Possíveis causas: 
   * Um invasor executou o reconhecimento para um ataque futuro.
   * Uma lógica de usuário ou aplicativo legítima explorou dados na conta de armazenamento.






## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [logs em contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-intro.md)
