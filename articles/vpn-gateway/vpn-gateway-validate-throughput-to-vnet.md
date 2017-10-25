---
title: "Validar a taxa de transferência VPN para uma Rede Virtual do Microsoft Azure | Microsoft Docs"
description: "A finalidade deste documento é ajudar um usuário a validar a taxa de transferência de rede de seus recursos locais para uma máquina virtual do Azure."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 3a1a6e2acd2ff40c2b35a6099f8a9fc7eb104bbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Como validar a taxa de transferência VPN para uma rede virtual

Uma conexão de gateway de VPN permite que você estabeleça conectividade entre instalações segura entre a sua Rede Virtual do Azure e sua infraestrutura de TI local.

Este artigo mostra como validar a taxa de transferência de rede dos recursos locais para uma máquina virtual (VM) do Azure. Ele também fornece diretrizes de solução de problemas.

>[!NOTE]
>Este artigo destina-se a ajudar a diagnosticar e corrigir problemas comuns. Se você não conseguir solucionar o problema usando as informações a seguir, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Visão geral

A conexão de gateway de VPN envolve os seguintes componentes:

- Dispositivo VPN local (exibir uma lista de [dispositivos VPN validados)](vpn-gateway-about-vpn-devices.md#devicetable).
- Internet pública
- Gateway de VPN do Azure
- VM do Azure

O diagrama a seguir mostra a conectividade lógica de uma rede local para uma rede virtual do Azure por meio de VPN.

![Conectividade Lógica da Rede do Cliente com a Rede MSFT usando VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcule a entrada/saída máxima esperada

1.  Determine os requisitos da taxa de transferência de linha de base do aplicativo.
2.  Determine os limites de taxa de transferência de gateway de VPN do Azure. Para obter ajuda, consulte a seção "Agregar taxa de transferência por tipo de VPN e SKU" do [Planejamento e design para Gateway de VPN](vpn-gateway-plan-design.md).
3.  Determine as [Diretrizes de taxa de transferência para a VM do Azure](../virtual-machines/virtual-machines-windows-sizes.md) para seu tamanho de VM.
4.  Determine a largura de banda do Provedor de Serviços de Internet (ISP).
5.  Calcule sua taxa de transferência esperada - Menor largura de banda de (VM, Gateway, ISP) * 0,8.

Se a taxa de transferência calculada não atender aos requisitos de taxa de transferência de linha de base do aplicativo, será necessário aumentar a largura de banda do recurso identificado como o afunilamento. Para redimensionar um Gateway de VPN do Azure, consulte [Alterar um SKU de gateway](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [Redimensionar uma VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Se não houver largura de banda de Internet esperada, também convém entrar em contato com seu ISP.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar a taxa de transferência de rede usando as ferramentas de desempenho

Essa validação deve ser executada durante o horário de pico, já que a saturação de taxa de transferência de túnel VPN durante o teste não apresenta resultados precisos.

A ferramenta que usamos para esse teste é iPerf, que funciona tanto no Windows como no Linux e tem modos de cliente e servidor. Ela é limitada a 3 Gbps para VMs do Windows.

Essa ferramenta não executa operações de leitura/gravação em disco. A ferramenta somente produz o tráfego TCP gerado automaticamente de uma extremidade à outra. As estatísticas são geradas com base em experimentos que medem a largura de banda disponível entre nós de cliente e servidor. Ao testar entre dois nós, um atua como o servidor e o outro como um cliente. Quando esse teste for concluído, é recomendável reverter as funções para testar tanto a taxa de transferência de upload como a de download em ambos os nós.

### <a name="download-iperf"></a>Baixar iPerf
Baixar [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para obter detalhes, consulte a [Documentação iPerf](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Os produtos de terceiros mencionados neste artigo são fabricados por empresas que são independentes da Microsoft. A Microsoft não oferece nenhuma garantia, implícita ou não, sobre o desempenho ou a confiabilidade desses produtos.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)
1. Habilite uma regra NSG/ACL permitindo o tráfego (para teste de endereço IP público em VM do Azure).

2. Em ambos os nós, habilite uma exceção de firewall para a porta 5001.

    **Windows:** Execute o seguinte comando como um administrador:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Para remover a regra após a conclusão do teste, execute esse comando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Linux do Azure:** as imagens do Linux do Azure têm firewalls permissivos. Se houver um aplicativo escutando em uma porta, o tráfego será permitido. Imagens personalizadas que são protegidas podem precisar de portas abertas explicitamente. Os firewalls da camada de OS do Linux comuns incluem `iptables`, `ufw`, ou `firewalld`.

3. No nó de servidor, altere para o diretório onde o iperf3.exe é extraído. Em seguida, execute o iPerf no modo de servidor e configure-o para escutar na porta 5001, de acordo com os seguintes comandos:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. No nó de cliente, altere para o diretório onde a ferramenta iperf é extraída e, em seguida, execute o seguinte comando:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    O cliente induz o tráfego na porta 5001 para o servidor por 30 segundos. O sinalizador '-P ' indica que estamos usando 32 conexões simultâneas para o nó de servidor.

    A tela a seguir mostra a saída a partir desse exemplo:

    ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCIONAL) Para preservar os resultados dos testes, execute este comando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Após concluir as etapas anteriores, execute as mesmas etapas com as funções invertidas, de modo que o nó de servidor agora seja o cliente e vice-versa.

## <a name="address-slow-file-copy-issues"></a>Solucionar problemas com cópia de arquivo lenta
É possível que você tenha experiência com cópia de arquivo lenta ao utilizar o Windows Explorer ou ao arrastar e soltar por meio de uma sessão RDP. Esse problema normalmente ocorre devido a um ou ambos os seguintes fatores:

- Os aplicativos de cópia de arquivo, como o Windows Explorer e o RDP não usam múltiplos threads ao copiar arquivos. Para obter melhor desempenho, use um aplicativo de cópia de arquivo multi-threaded como o [Richcopy](https://technet.microsoft.com/en-us/magazine/2009.04.utilityspotlight.aspx) para copiar arquivos usando 16 ou 32 threads. Para alterar o número de thread para cópia de arquivo no Richcopy, clique em **Ação** > **Opções de cópia** > **Cópia de arquivos**.<br><br>
![Problemas com cópia de arquivo lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Velocidade de leitura/gravação de disco de VM insuficiente. Para obter mais informações, consulte [Solução de Problemas de Armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface externa do dispositivo local
Se o endereço IP para a Internet do dispositivo VPN local estiver incluído na definição [rede local](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) no Azure, você poderá enfrentar a impossibilidade de conectar o VPN, problemas de desempenho ou desconexões esporádicas.

## <a name="checking-latency"></a>Verificação de latência
Use o tracert para rastrear o dispositivo Microsoft Azure Edge e determinar se há atrasos superiores a 100 ms entre saltos.

Na rede local, execute *tracert* para o VIP do Gateway Azure ou VM. Após visualizar somente o * retornado, você saberá que atingiu o limite do Azure. Quando nomes DNS que incluem "MSN" forem retornados, você saberá que atingiu o backbone da Microsoft.<br><br>
![Verificação de Latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações ou ajuda, confira os seguintes links:

- [Otimizar a taxa de transferência de rede para máquinas virtuais do Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
