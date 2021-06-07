---
title: Solucionar problemas de conectividade do Azure para a recuperação de desastre do Azure com o Azure Site Recovery
description: Solucionar problemas de conectividade na recuperação de desastre de VM do Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652255"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Solucionar problemas de conectividade de rede de VM do Azure para Azure

Este artigo descreve os problemas comuns relacionados à conectividade de rede quando você replica e recupera máquinas virtuais (VM) do Azure de uma região para outra região. Para obter mais informações sobre os requisitos de rede, consulte os [requisitos de conectividade para replicar VMs do Azure](azure-to-azure-about-networking.md).

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas.

| **Nome**                  | **Comercial**                               | **Governo**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM. Se você souber todas as contas de armazenamento em cache para suas VMs, poderá usar uma lista de permissões para as URLs de conta de armazenamento específicas. Por exemplo, `cache1.blob.core.windows.net` e `cache2.blob.core.windows.net` em vez de `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM. Você pode usar o _IP site Recovery_ correspondente se o proxy de firewall oferecer suporte a IPS. |
| Barramento de Serviço               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM. Você pode usar o _IP de monitoramento de site Recovery_ correspondente se o proxy de firewall oferecer suporte a IPS. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problema 1: falha ao registrar a máquina virtual do Azure com o Site Recovery (151195)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma conexão com Site Recovery pontos de extremidade devido a uma falha de resolução de DNS (sistema de nomes de domínio). Esse problema é mais comum durante a nova proteção quando você faz failover da VM, mas o servidor DNS não está acessível da região de recuperação de desastres (DR).

#### <a name="resolution"></a>Resolução

Se você estiver usando o DNS personalizado, verifique se o servidor DNS está acessível na região de recuperação de desastre.

Para verificar se a VM usa uma configuração de DNS personalizada:

1. Abra as **máquinas virtuais** e selecione a VM.
1. Navegue até as **configurações** de VMs e selecione **rede**.
1. Em **rede virtual/sub-rede**, selecione o link para abrir a página de recursos da rede virtual.
1. Vá para **configurações** e selecione **servidores DNS**.

Tente acessar o servidor DNS da máquina virtual. Se o servidor DNS não estiver acessível, torne-o acessível ao fazer failover do servidor DNS ou criar a linha de site entre a rede de DR e o DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-erro":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: falha na configuração do Azure Site Recovery (151196)

> [!NOTE]
> Se as VMs estiverem atrás de um balanceador de carga interno **padrão** , por padrão, não teria acesso aos IPs de Microsoft 365, como `login.microsoftonline.com` . Altere-o para o tipo **básico** de balanceador de carga interno ou crie acesso de saída conforme mencionado no artigo [Configurar o balanceamento de carga e as regras de saída no Standard Load Balancer usando CLI do Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration).

#### <a name="possible-cause"></a>Causa possível

Uma conexão não pode ser estabelecida para Microsoft 365 pontos de extremidade de autenticação e identidade IP4.

#### <a name="resolution"></a>Resolução

- Azure Site Recovery requer acesso aos intervalos de IP de Microsoft 365 para autenticação.
- Se você estiver usando regras de NSG (grupo de segurança de rede) do Azure/proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de permitir a comunicação com os intervalos de IP Microsoft 365. Crie Azure Active Directory uma regra de NSG baseada em [marca de serviço (AD do Azure)](../virtual-network/network-security-groups-overview.md#service-tags) que permita o acesso a todos os endereços IP correspondentes ao Azure AD.
- Se novos endereços forem adicionados ao Azure AD no futuro, você precisará criar novas regras de NSG.

### <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você estiver usando regras NSG para controlar a conectividade de saída, use permitir regras de **saída https** para a porta 443 para todos os intervalos de endereços IP necessários.
- O exemplo supõe que o local de origem da VM é **leste dos EUA** e o local de destino é **EUA Central**.

#### <a name="nsg-rules---east-us"></a>Regras de NSG – Leste dos EUA

1. Crie uma regra de segurança de saída HTTPS para o NSG, conforme mostrado na captura de tela a seguir. Este exemplo usa a **marca de serviço de destino**: _Storage. eastus_ e os **intervalos de porta de destino**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Captura de tela mostra um painel Adicionar regra de segurança de saída para uma regra de segurança para o ponto de armazenamento East S.":::

1. Crie uma regra de segurança de saída HTTPS para o NSG, conforme mostrado na captura de tela a seguir. Este exemplo usa a **marca de serviço de destino**: _AzureActiveDirectory_ e intervalos de **porta de destino**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Captura de tela mostra um painel Adicionar regra de segurança de saída para uma regra de segurança para Azure Active Directory.":::

1. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Centralus" no NSG que corresponde ao local de destino. Isso permite o acesso ao monitoramento de Site Recovery.
1. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

#### <a name="nsg-rules---central-us"></a>Regras de NSG – EUA Central

Para este exemplo, essas regras NSG são necessárias para que a replicação possa ser habilitada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança de saída HTTPS para _Storage. centralus_:

   - **Marca de serviço de destino**: _Storage. centralus_
   - **Intervalos de portas de destino**: _443_

1. Crie uma regra de segurança de saída HTTPS para _AzureActiveDirectory_.

   - **Marca de serviço de destino**: _AzureActiveDirectory_
   - **Intervalos de portas de destino**: _443_

1. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Eastus" no NSG que corresponde ao local de origem. Isso permite o acesso ao monitoramento de Site Recovery.
1. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: falha na configuração do Site Recovery (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma conexão com Azure Site Recovery pontos de extremidade de serviço.

#### <a name="resolution"></a>Resolução

Se você estiver usando um proxy de Firewall/regra do NSG (grupo de segurança de rede) do Azure para controlar a conectividade de rede de saída no computador, haverá várias marcas de serviço que precisam ser permitidas. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: falha na replicação do Azure para o Azure quando o tráfego de rede passa pelo servidor proxy local (151072)

#### <a name="possible-cause"></a>Causa possível

As configurações de proxy personalizadas são inválidas e o agente do serviço de mobilidade Azure Site Recovery não fez a detecção automática das configurações de proxy do Internet Explorer (IE).

#### <a name="resolution"></a>Resolução

1. O agente do serviço de mobilidade detecta as configurações de proxy do IE no Windows e `/etc/environment` no Linux.
1. Se preferir definir proxy somente para Azure Site Recovery serviço de mobilidade, você poderá fornecer os detalhes do proxy em _ProxyInfo. conf_ localizado em:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo. conf_ deve ter as configurações de proxy no seguinte formato _ini_ :

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery agente do serviço de mobilidade dá suporte apenas a **proxies não autenticados**.

### <a name="fix-the-problem"></a>Corrigir o problema

Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), siga as etapas no [documento diretrizes de rede](./azure-to-azure-about-networking.md).

## <a name="next-steps"></a>Próximas etapas

[Replicar VMs do Azure para outra região do Azure](azure-to-azure-how-to-enable-replication.md)