---
title: Solução de problemas de solução de virtualização de rede no Azure | Microsoft Docs
description: Solucionar problemas de NVA (solução de virtualização de rede) no Azure e validar os requisitos básicos da plataforma Azure para configurações do NVA.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99095634"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemas de solução de virtualização de rede no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode enfrentar uma VM ou problemas de conectividade VPN e erros ao usar um terceiro dispositivo Virtual de rede (NVA) no Microsoft Azure de terceiros. Este artigo fornece as etapas básicas para ajudá-lo a validar requisitos básicos de plataforma do Azure para configurações de NVA.

Suporte técnico para NVAs de terceiros e sua integração com a plataforma do Azure é fornecido pelo fornecedor NVA.

> [!NOTE]
> Se você tiver um problema de roteamento que envolve uma NVA ou de conectividade, você deve [entrar em contato com o fornecedor da NVA](https://mskb.pkisolutions.com/kb/2984655) diretamente.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de verificação de solução de problemas com o fornecedor NVA

- Atualizações de software para o software de VM NVA
- Funcionalidade e configuração da conta de serviço
- Rotas definidas pelo usuário (UDRs) em sub-redes da rede virtual que direcionam o tráfego para NVA
- UDRs em sub-redes da rede virtual que direcionam o tráfego de NVA
- Tabelas de roteamento e regras dentro da NVA (por exemplo, de NIC1 à NIC2)
- Rastreamento em NICs de NVA para verificar a receber e enviar tráfego de rede
- Ao usar um SKU Standard e IPs públicos, deve haver um NSG criado e uma regra explícita para permitir que o tráfego seja roteado para o NVA.

## <a name="basic-troubleshooting-steps"></a>Etapas para solucionar problemas

- Verificar a configuração básica
- Verificar o desempenho de NVA
- Solução de problemas de rede avançada

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Verificar os requisitos mínimos de configuração para NVAs no Azure

Cada NVA tem requisitos de configuração básica para funcionar corretamente no Azure. A seção a seguir fornece as etapas para verificar essas configurações básicas. Para obter mais informações, [entre em contato com o fornecedor da NVA](https://mskb.pkisolutions.com/kb/2984655).

**Verifique se o encaminhamento de IP está habilitado na NVA**

Usar o portal do Azure

1. Localize o recurso NVA na [portal do Azure](https://portal.azure.com), selecione a rede e, em seguida, selecione o adaptador de rede.
2. Na página Interface de rede, selecione Configurações de IP.
3. Certifique-se de que o encaminhamento de IP está habilitado.

Usar o PowerShell

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.
2. Execute o seguinte comando (substitua os valores entre colchetes com suas informações):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Verifique a propriedade **EnableIPForwarding**.
4. Se o encaminhamento de IP não estiver habilitado, execute os seguintes comandos para habilitá-lo:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Verificar NSG ao usar o IP PUBILC SKU padrão** Ao usar um SKU Standard e IPs públicos, deve haver um NSG criado e uma regra explícita para permitir o tráfego para o NVA.

**Verifique se o tráfego possa ser roteado para a NVA**

1. No [portal do Azure](https://portal.azure.com), abra **Observador de Rede**, selecione **Próximo Salto**.
2. Especifique uma VM configurada para redirecionar o tráfego para o NVA e um endereço IP de destino no qual exibir o próximo salto. 
3. Se a NVA não estiver listada como o **próximo salto**, verifique e atualize as tabelas de rotas do Azure.

**Verifique se o tráfego pode alcançar a NVA**

1. No [portal do Azure](https://portal.azure.com), abra **Observador de Rede** e, em seguida, selecione **Verificação do IP de Fluxo**. 
2. Especifique a VM e o endereço IP da NVA e, em seguida, verifique se o tráfego está bloqueado por quaisquer grupos de segurança de rede (NSG).
3. Se houver uma regra NSG que bloqueia o tráfego, localize a NSG em regras de **segurança em vigor** e, em seguida, atualize-o para permitir a passagem do tráfego. Em seguida, execute a **Verificação de Fluxo de IP** novamente e use **Solucionar problemas de conexão** para testar a comunicação de TCP da VM para seu endereço IP interno ou externo.

**Verifique se a NVA e VMs estão escutando para tráfego esperado**

1. Conectar-se para a NVA usando RDP ou SSH e, em seguida, execute o seguinte comando:

    Para Windows:

    ```console
   netstat -an
    ```

    Para Linux:

    ```console
   netstat -an | grep -i listen
    ```
2. Se você não vir a porta TCP usada pelo software de NVA que está listado nos resultados, você deve configurar o aplicativo sobre o NVA e a VM para ouvir e responder ao tráfego que chega a essas portas. [Entre em contato com o fornecedor NVA para obter assistência conforme necessário](https://mskb.pkisolutions.com/kb/2984655).

## <a name="check-nva-performance"></a>Verificar o desempenho de NVA

### <a name="validate-vm-cpu"></a>Validar a CPU da VM

Se o uso da CPU ficar perto de 100%, você poderá enfrentar problemas que afetam os descartes de pacotes de rede. Seus relatórios VM médios de CPU para um período de tempo específico no portal do Azure. Durante um pico de CPU, investigar qual processo no convidado que VM está causando a alta utilização da CPU e resolvê-lo, se possível. Você também terá que redimensionar a VM para um tamanho maior de SKU ou, para o conjunto de dimensionamento de máquina virtual, aumentar a contagem de instâncias ou definido para dimensionamento automático no uso da CPU. Para qualquer um desses problemas, [entre em contato com o fornecedor do NVA para obter assistência](https://mskb.pkisolutions.com/kb/2984655), conforme necessário.

### <a name="validate-vm-network-statistics"></a>Validar as estatísticas de rede de VM

Se a rede VM usa picos ou mostra períodos de alto uso, que você também pode ter que aumentar o tamanho do SKU da VM para obter os recursos de taxa de transferência mais alta. Também é possível reimplantar a VM tendo a Rede Acelerada habilitada. Para verificar se a NVA dá suporte a recurso de rede acelerada [entre em contato com o fornecedor NVA para obter assistência](https://mskb.pkisolutions.com/kb/2984655), conforme necessário.

## <a name="advanced-network-administrator-troubleshooting"></a>Administrador de rede avançados de solução de problemas

### <a name="capture-network-trace"></a>Capturar o rastreamento de rede
Capturar um rastreamento de rede simultânea na VM de origem, a NVA, e a VM de destino ao executar **[PsPing](/sysinternals/downloads/psping)** ou **Nmap**, e, em seguida, pare o rastreamento.

1. Para capturar um rastreamento de rede simultâneas, execute o seguinte comando:

   **Para Windows**

   netsh trace Start Capture = Sim TraceFile = c:\ server_IP. etl cenário = NetConnection

   **Para Linux**

   sudo tcpdump-S0-i eth0-X-w vmtrace. Cap

2. Use **PsPing** ou **Nmap** da VM de origem para a VM de destino (por exemplo: `PsPing 10.0.0.4:80` ou `Nmap -p 80 10.0.0.4`).
3. Abra o rastreamento de rede de destino de VM usando [Monitor de Rede da Microsoft](https://download.cnet.com/s/network-monitor) ou tcpdump. Aplicar um filtro de exibição para o IP da VM de origem que você executou **PsPing** ou **Nmap**, como `IPv4.address==10.0.0.4 (Windows netmon)` ou `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analisar rastreamentos

Se você não vir a entrada de pacotes para o rastreamento de VM de back-end, provavelmente haverá um NSG/UDR interferindo ou as tabelas de roteamento NVA estão incorretas.

Se você vir os pacotes que chegam mas sem resposta, precisará solucionar um problema de firewall ou de um aplicativo de VM. Para qualquer um desses problemas, [entre em contato com o fornecedor para obter assistência, conforme necessário](https://mskb.pkisolutions.com/kb/2984655).