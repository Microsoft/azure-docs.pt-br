---
title: Usar o recurso diagnosticar conectividade no Integration Runtime do SSIS
description: Solucionar problemas de conexão no tempo de execução de integração do SSIS usando o recurso diagnosticar conectividade.
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361845"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Usar o recurso diagnosticar conectividade no Integration Runtime do SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Você pode encontrar problemas de conectividade ao executar pacotes do SQL Server Integration Services (SSIS) no Integration Runtime do SSIS. Esses problemas ocorrem especialmente se o Integration Runtime do SSIS ingressar na rede virtual do Azure.

Solucionar problemas de conectividade usando o recurso *diagnosticar conectividade* para testar conexões. O recurso está localizado na página monitoramento do tempo de execução de integração do SSIS do portal de Azure Data Factory.

 ![Página monitorar-diagnosticar conectividade](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Página monitorar-testar conexão](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

Use as seções a seguir para saber mais sobre os erros mais comuns que ocorrem quando você está testando conexões. Cada seção descreve o:

- Código do erro
- Mensagem de erro
- Causa (s) potencial (ões) do erro
- Solução (ões) recomendada (s)

## <a name="error-code-invalidinput"></a>Código de erro: InvalidInput

- **Mensagem de erro**: "Verifique se sua entrada está correta".
- **Causa potencial**: sua entrada está incorreta.
- **Recomendação**: Verifique sua entrada.

## <a name="error-code-firewallornetworkissue"></a>Código de erro: FirewallOrNetworkIssue

- **Mensagem de erro**: "Verifique se essa porta está aberta no seu firewall/servidor/NSG e a rede está estável".
- **Possíveis causas:**
  - O servidor não abre a porta.
  - Seu grupo de segurança de rede tem o tráfego de saída negado na porta.
  - Seu firewall do NVA/firewall/local do Azure não abre a porta.
- **Recommendations**
  - Abra a porta no servidor.
  - Atualize o grupo de segurança de rede para permitir o tráfego de saída na porta.
  - Abra a porta no firewall do NVA/Firewall do Azure/local.

## <a name="error-code-misconfigureddnssettings"></a>Código de erro: MisconfiguredDnsSettings

- **Mensagem de erro**: "se você estiver usando seu próprio servidor DNS na VNet ingressado pelo seu Azure-SSIS ir, verifique se ele pode resolver o nome do host".
- **Possíveis causas:**
  -  Há um problema com o DNS personalizado.
  -  Você não está usando um FQDN (nome de domínio totalmente qualificado) para seu nome de host privado.
- **Recommendations**
  -  Corrija seu problema de DNS personalizado para certificar-se de que ele pode resolver o nome do host.
  -  Use o FQDN. Azure-SSIS IR não acrescentará automaticamente seu próprio sufixo DNS. Por exemplo, use **<your_private_server>. contoso.com** em vez de **<your_private_server>**.

## <a name="error-code-servernotallowremoteconnection"></a>Código de erro: ServerNotAllowRemoteConnection

- **Mensagem de erro**: "Verifique se o servidor permite conexões TCP remotas por meio desta porta."
- **Possíveis causas:**
  -  O Firewall do servidor não permite conexões TCP remotas.
  -  O servidor não está online.
- **Recommendations**
  -  Permitir conexões TCP remotas no firewall do servidor.
  -  Inicie o servidor.
   
## <a name="error-code-misconfigurednsgsettings"></a>Código de erro: MisconfiguredNsgSettings

- **Mensagem de erro**: "Verifique se o NSG da sua VNet permite o tráfego de saída por essa porta. Se você estiver usando o Azure ExpressRoute e o ou um UDR, verifique se essa porta está aberta no seu firewall/servidor. "
- **Possíveis causas:**
  -  Seu grupo de segurança de rede tem o tráfego de saída negado na porta.
  -  Seu firewall do NVA/firewall/local do Azure não abre a porta.
- **Recomendação:**
  -  Atualize o grupo de segurança de rede para permitir o tráfego de saída na porta.
  -  Abra a porta no firewall do NVA/Firewall do Azure/local.

## <a name="error-code-genericissues"></a>Código de erro: GenericIssues

- **Mensagem de erro**: "falha no teste de conexão devido a problemas genéricos".
- **Causa potencial**: a conexão de teste encontrou um problema temporário geral.
- **Recomendação**: repita a conexão de teste mais tarde. Se tentar novamente não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Código de erro: PSPingExecutionTimeout

- **Mensagem de erro**: "tempo limite de conexão de teste, tente novamente mais tarde".
- **Causa potencial**: o tempo limite de conectividade de teste expirou.
- **Recomendação**: repita a conexão de teste mais tarde. Se tentar novamente não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="error-code-networkinstable"></a>Código de erro: NetworkInstable

- **Mensagem de erro**: "testar conexão irregular com êxito devido à instabilidade da rede".
- **Causa potencial**: problema de rede transitório.
- **Recomendação**: Verifique se a rede do servidor ou do firewall está estável.

## <a name="next-steps"></a>Próximas etapas

- [Implantar um projeto do SSIS no Azure com o SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Executar pacotes do SSIS no Azure com o SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [Agendar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)