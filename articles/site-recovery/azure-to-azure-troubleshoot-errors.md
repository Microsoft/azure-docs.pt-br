---
title: Solucionar problemas de replicação de VM do Azure no Azure Site Recovery
description: Solucionar erros ao replicar máquinas virtuais do Azure para recuperação de desastre.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900409"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Solucionar problemas de erros de replicação de VMs do Azure para o Azure

Este artigo descreve como solucionar erros comuns no Azure Site Recovery durante a replicação e a recuperação de máquinas virtuais (VM) do Azure de uma região para outra. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)

Verifique se sua assinatura está habilitada para criar VMs do Azure na região de destino que você planeja usar como sua região de recuperação de desastres (DR). Sua assinatura precisa de cota suficiente para criar VMs dos tamanhos necessários. Por padrão, Site Recovery escolhe um tamanho de VM de destino que seja igual ao tamanho da VM de origem. Se o tamanho correspondente não estiver disponível, Site Recovery escolherá automaticamente o tamanho mais próximo disponível.

Se não houver um tamanho que dê suporte à configuração da VM de origem, a seguinte mensagem será exibida:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Possíveis causas

- Sua ID de assinatura não está habilitada para criar VMs no local da região de destino.
- Sua ID de assinatura não está habilitada ou não tem cota suficiente para criar tamanhos de VM específicos no local da região de destino.
- Nenhum tamanho de VM de destino adequado é encontrado para corresponder à contagem de NIC (placa de interface de rede) da VM de origem (2), para a ID da assinatura no local da região de destino.

### <a name="fix-the-problem"></a>Corrigir o problema

Contate o [suporte de cobrança do Azure](../azure-portal/supportability/resource-manager-core-quotas-request.md) para habilitar sua assinatura para criar VMs dos tamanhos necessários no local de destino. Em seguida, repita a operação com falha.

Se o local de destino tiver uma restrição de capacidade, desabilite a replicação para esse local. Em seguida, habilite a replicação para um local diferente em que sua assinatura tem uma cota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz confiável (código de erro 151066)

Se nem todos os certificados raiz confiáveis mais recentes estiverem presentes na VM, o trabalho para habilitar a replicação para Site Recovery poderá falhar. A autenticação e a autorização de chamadas de serviço Site Recovery da VM falham sem esses certificados.

Se o trabalho habilitar replicação falhar, a seguinte mensagem será exibida:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Causa possível

Os certificados raiz confiáveis necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

#### <a name="windows"></a>Windows

Para uma VM que executa o sistema operacional Windows, instale as atualizações mais recentes do Windows para que todos os certificados raiz confiáveis estejam presentes na VM. Siga o processo típico de gerenciamento de atualização de certificado do Windows ou gerenciamento de atualizações em sua organização para obter os certificados raiz mais recentes e a lista de certificados revogados atualizados nas VMs.

- Se você estiver em um ambiente desconectado, siga o processo de atualização padrão do Windows em sua organização para obter os certificados.
- Se os certificados necessários não estiverem presentes na VM, as chamadas para o serviço de recuperação de Site falharão por razões de segurança.

Para verificar se o problema foi resolvido, vá para `login.microsoftonline.com` de um navegador em sua VM.

Para obter mais informações, consulte [Configurar raízes confiáveis e certificados não permitidos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da sua versão do sistema operacional Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados mais recente na VM.

Como o SUSE Linux usa links simbólicos, ou symlinks, para manter uma lista de certificados, siga estas etapas:

1. Entre como um usuário **raiz** . O símbolo de hash ( `#` ) é o prompt de comando padrão.

1. Para alterar o diretório, execute este comando:

   `cd /etc/ssl/certs`

1. Verifique se o certificado de AC raiz da Symantec está presente:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Se o certificado de AC raiz da Symantec não for encontrado, execute o comando a seguir para baixar o arquivo. Verifique se há erros e siga as ações recomendadas para falhas de rede.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Verifique se o certificado de autoridade de certificação raiz do Baltimore está presente:

   `ls Baltimore_CyberTrust_Root.pem`

   - Se o certificado de autoridade de certificação raiz do Baltimore não for encontrado, execute este comando para baixar o certificado:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

   `ls DigiCert_Global_Root_CA.pem`

    - Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para baixar o certificado:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Para atualizar os hashes de entidade do certificado para os certificados recentemente baixados, execute o script de rehash:

   `c_rehash`

1. Para verificar se os hashes da entidade como symlinks foram criados para os certificados, execute estes comandos:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Crie uma cópia do arquivo _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ com o nome de arquivo _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Crie uma cópia do arquivo _Baltimore_CyberTrust_Root. pem_ com o nome de arquivo _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Crie uma cópia do arquivo _DigiCert_Global_Root_CA. pem_ com o nome de arquivo _3513523f. 0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Verifique se os arquivos estão presentes:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Intervalos de IP ou URLs de saída (código de erro 151037 ou 151072)

Para que a replicação de Site Recovery funcione, a conectividade de saída para URLs específicas é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas. Enquanto continuamos a dar suporte ao acesso de saída por meio de URLs, não há mais suporte para o uso de uma lista de permissões de intervalos de IP.

#### <a name="possible-causes"></a>Possíveis causas

- Não é possível estabelecer uma conexão com Site Recovery pontos de extremidade devido a uma falha de resolução de DNS (sistema de nomes de domínio).
- Esse problema é mais comum durante a nova proteção quando você faz failover da máquina virtual, mas o servidor DNS não está acessível da região de recuperação de desastres (DR).

#### <a name="fix-the-problem"></a>Corrigir o problema

Se você estiver usando o DNS personalizado, verifique se o servidor DNS está acessível na região de recuperação de desastre.

Para verificar se a VM usa uma configuração de DNS personalizada:

1. Abra as **máquinas virtuais** e selecione a VM.
1. Navegue até as **configurações** de VMs e selecione **rede**.
1. Em **rede virtual/sub-rede**, selecione o link para abrir a página de recursos da rede virtual.
1. Vá para **configurações** e selecione **servidores DNS**.

Tente acessar o servidor DNS da máquina virtual. Se o servidor DNS não estiver acessível, torne-o acessível ao fazer failover do servidor DNS ou criar a linha de site entre a rede de DR e o DNS.

> [!NOTE]
> Se você usar pontos de extremidade privados, verifique se as VMs podem resolver os registros DNS privados.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="erro com.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: falha na configuração do Azure Site Recovery (151196)

#### <a name="possible-cause"></a>Causa possível

Uma conexão não pode ser estabelecida para Microsoft 365 pontos de extremidade de autenticação e identidade IP4.

#### <a name="fix-the-problem"></a>Corrigir o problema

Azure Site Recovery acesso necessário para Microsoft 365 intervalos de IP para autenticação.
Se você estiver usando regras de NSG (grupo de segurança de rede) do Azure/proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de usar a NSG da [marca de serviço do AAD (Azure Active Directory)](../virtual-network/network-security-groups-overview.md#service-tags) para permitir o acesso ao AAD. Não há mais suporte para regras NSG baseadas em endereço IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: falha na configuração do Site Recovery (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma conexão com Azure Site Recovery pontos de extremidade de serviço.

#### <a name="fix-the-problem"></a>Corrigir o problema

Se você estiver usando regras de NSG (grupo de segurança de rede) do Azure/proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de usar marcas de serviço. Não há mais suporte para usar uma lista de permissões de endereços IP via NSGs para Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problema 4: a replicação falha quando o tráfego de rede usa o servidor proxy local (151072)

#### <a name="possible-cause"></a>Causa possível

As configurações de proxy personalizadas são inválidas e o agente do serviço de mobilidade não autodetectaram as configurações de proxy do Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Corrigir o problema

1. O agente do serviço de mobilidade detecta as configurações de proxy do IE no Windows e `/etc/environment` no Linux.
1. Se você preferir definir o proxy somente para o serviço de mobilidade, poderá fornecer os detalhes do proxy em _ProxyInfo. conf_ localizado em:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo. conf_ deve ter as configurações de proxy no seguinte formato _ini_ .

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> O agente do serviço de mobilidade dá suporte apenas a **proxies não autenticados**.

### <a name="more-information"></a>Mais informações

Para especificar as [URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), siga as orientações em [sobre a rede na replicação do Azure para o Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Disco não encontrado na VM (código de erro 150039)

Um novo disco anexado à máquina virtual deve ser inicializado. Se o disco não for encontrado, a seguinte mensagem será exibida:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi anexado à VM, mas não foi inicializado.
- O disco de dados dentro da VM não está relatando corretamente o valor de LUN (número de unidade lógica) no qual o disco foi anexado à VM.

### <a name="fix-the-problem"></a>Corrigir o problema

Verifique se os discos de dados foram inicializados e repita a operação.

- **Windows**: [anexe e inicialize um novo disco](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [inicializar um novo disco de dados no Linux](../virtual-machines/linux/add-disk.md).

Se o problema persistir, contate o Suporte.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Vários discos disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram adicionados recentemente à máquina virtual após a proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

Para tornar o status de replicação da VM íntegro novamente, você pode optar por proteger os discos ou ignorar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Vá para **itens replicados**  >  _nome da VM_  >  **discos**.
1. Selecione o disco desprotegido e, em seguida, selecione **habilitar replicação**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Habilite a replicação em discos de VM.":::

#### <a name="to-dismiss-the-warning"></a>Para ignorar o aviso

1. Vá para **itens replicados**  >  _nome da VM_.
1. Selecione o aviso na seção **visão geral** e, em seguida, selecione **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Ignorar aviso de novo disco.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>A VM removida do cofre foi concluída com informações (código de erro 150225)

Quando Site Recovery protege a máquina virtual, ele cria links na máquina virtual de origem. Quando você remove a proteção ou desabilita a replicação, Site Recovery remove esses links como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recurso, o trabalho de limpeza será concluído com as informações. As informações indicam que a máquina virtual foi removida do cofre dos serviços de recuperação, mas que alguns dos links obsoletos não puderam ser limpos no computador de origem.

Você pode ignorar esse aviso se você nunca pretender proteger essa máquina virtual novamente. Mas se você precisar proteger essa máquina virtual posteriormente, siga as etapas nesta seção para limpar os links.

> [!WARNING]
> Se você não fizer a limpeza:
>
> - Quando você habilita a replicação por meio do cofre dos serviços de recuperação, a máquina virtual não será listada.
> - Se você tentar proteger a VM usando as configurações de **máquina virtual**  >    >  **recuperação de desastre**, a operação falhará com a **replicação de mensagem não pode ser habilitada devido aos links de recursos obsoletos existentes na VM**.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM chamada `MoveDemo` deve ser excluído:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Remova o bloqueio da VM.":::

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça a **ID da assinatura**, o **grupo de recursos da VM** e o nome da **VM** como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replicação não habilitada na VM com recursos obsoletos (código de erro 150226)

### <a name="possible-causes"></a>Possíveis causas

A máquina virtual tem uma configuração obsoleta da proteção de Site Recovery anterior.

Uma configuração obsoleta pode ocorrer em uma VM do Azure se você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida:

- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recurso.
- Você excluiu o cofre Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de Site Recovery sem desabilitar explicitamente a replicação na VM.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM chamada `MoveDemo` deve ser excluído:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Remova o bloqueio da VM.":::

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça a **ID da assinatura**, o **grupo de recursos da VM** e o nome da **VM** como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Não é possível selecionar VM ou grupo de recursos em Habilitar trabalho de replicação

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problema 1: o grupo de recursos e a VM de origem estão em locais diferentes

Atualmente, Site Recovery requer que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, você não poderá encontrar a máquina virtual ou o grupo de recursos ao tentar aplicar a proteção.

Como alternativa, você pode habilitar a replicação da VM em vez do cofre dos serviços de recuperação. Vá para **origem VM**  >  **Propriedades**  >  **recuperação de desastre** e habilite a replicação.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problema 2: o grupo de recursos não faz parte da assinatura selecionada

Talvez você não consiga localizar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da assinatura selecionada. Verifique se o grupo de recursos pertence à assinatura que você está usando.

### <a name="issue-3-stale-configuration"></a>Problema 3: configuração obsoleta

Talvez você não veja a VM que deseja habilitar para replicação se houver uma configuração de Site Recovery obsoleta na VM do Azure. Essa condição pode ocorrer se você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida:

- Você excluiu o cofre Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recurso.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Certifique-se de atualizar o `AzureRM.Resources` módulo antes de usar o script mencionado nesta seção. Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio, se houver, do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM chamada `MoveDemo` deve ser excluído:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Remova o bloqueio da VM.":::

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça a **ID da assinatura**, o **grupo de recursos da VM** e o nome da **VM** como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="unable-to-select-a-vm-for-protection"></a>Não é possível selecionar uma VM para proteção

### <a name="possible-cause"></a>Causa possível

A máquina virtual tem uma extensão instalada em um estado com falha ou sem resposta

### <a name="fix-the-problem"></a>Corrigir o problema

Vá para **máquinas virtuais**  >  **configurações**  >  **extensões** e verifique se há extensões em estado de falha. Desinstale qualquer extensão com falha e tente proteger a máquina virtual novamente.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para habilitar a replicação na VM, seu estado de provisionamento deve ser **bem-sucedido**. Siga estas etapas para verificar o estado de provisionamento:

1. Na portal do Azure, selecione o **Gerenciador de recursos** em **todos os serviços**.
1. Expanda a lista **Assinaturas** e selecione sua assinatura.
1. Expanda a lista **ResourceGroups** e selecione o grupo de recursos da VM.
1. Expanda a lista de **recursos** e selecione sua VM.
1. Verifique o campo **provisioningState** na exibição de instância no lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se o **provisioningState** tiver **falhado**, entre em contato com o suporte com detalhes para solucionar problemas.
- Se o **provisioningState** estiver sendo **atualizado**, outra extensão poderá estar sendo implantada. Verifique se há alguma operação em andamento na VM, aguarde até que elas sejam concluídas e repita o trabalho de Site Recovery com falha para habilitar a replicação.

## <a name="unable-to-select-target-vm"></a>Não é possível selecionar a VM de destino

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problema 1: a VM está conectada a uma rede que já está mapeada para uma rede de destino

Durante a configuração de recuperação de desastre, se a VM de origem fizer parte de uma rede virtual e outra VM da mesma rede virtual já estiver mapeada com uma rede no grupo de recursos de destino, a caixa de listagem suspensa seleção de rede estará indisponível (aparece esmaecida) por padrão.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lista de seleção de rede indisponível.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problema 2: você protegeu anteriormente a VM e, em seguida, desabilitou a replicação

Desabilitar a replicação de uma VM não exclui o mapeamento de rede. O mapeamento deve ser excluído do cofre dos serviços de recuperação onde a VM foi protegida. Selecione o **cofre dos serviços de recuperação** e vá para **gerenciar** a  >  **infraestrutura**  >  de site Recovery para o mapeamento **de rede de máquinas virtuais do Azure**  >  .

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Excluir mapeamento de rede.":::

A rede de destino que foi configurada durante a configuração de recuperação de desastre pode ser alterada após a configuração inicial e, depois que a VM é protegida. Para **Modificar o mapeamento de rede** , selecione o nome da rede:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modificar o mapeamento de rede.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ ou VSS (código de erro 151025)

Quando ocorre o erro de COM+ ou Serviço de Cópias de Sombra de Volume (VSS), a seguinte mensagem é exibida:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicativo do sistema COM+ está desabilitado.
- O Serviço de Cópias de Sombra de Volume está desabilitado.

### <a name="fix-the-problem"></a>Corrigir o problema

Defina o aplicativo do sistema COM+ e Serviço de Cópias de Sombra de Volume como modo de inicialização automático ou manual.

1. Abra o console de serviços no Windows.
1. Verifique se o aplicativo do sistema COM+ e os Serviço de Cópias de Sombra de Volume não estão definidos como **desabilitados** como seu **tipo de inicialização**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Verifique o tipo de inicialização de COM mais aplicativo do sistema e Serviço de Cópias de Sombra de Volume.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho de disco gerenciado sem suporte (código de erro 150172)

Quando esse erro ocorre, a seguinte mensagem é exibida:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Causa possível

O disco é menor do que o tamanho com suporte de 1024 MB.

### <a name="fix-the-problem"></a>Corrigir o problema

Verifique se o tamanho do disco está dentro do intervalo de tamanho com suporte e repita a operação.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Proteção não habilitada quando o GRUB usa o nome do dispositivo (código de erro 151126)

### <a name="possible-causes"></a>Possíveis causas

Os arquivos de configuração do GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/Grub2/grub.cfg_ ou _/etc/default/grub_) do Linux de Unificação geral podem especificar os nomes de dispositivo reais em vez de valores de UUID (identificador universal exclusivo) para os `root` `resume` parâmetros e. Site Recovery requer UUIDs porque os nomes de dispositivo podem ser alterados. Após a reinicialização, uma VM pode não vir com o mesmo nome no failover, resultando em problemas.

Os exemplos a seguir são linhas de arquivos GRUB nos quais os nomes de dispositivo aparecem em vez dos UUIDs necessários:

- _/Boot/Grub2/grub.cfg_ do arquivo:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Arquivo: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Corrigir o problema

Substitua cada nome de dispositivo pelo UUID correspondente:

1. Localize o UUID do dispositivo executando o comando `blkid <device name>` . Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo UUID, nos formatos `root=UUID=<UUID>` e `resume=UUID=<UUID>` . Por exemplo, após a substituição, a linha de _/boot/grub/menu.lst_ ficaria parecida com a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Tente novamente a proteção.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Falha na proteção porque o dispositivo GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Causa possível

Os arquivos de configuração do GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/Grub2/grub.cfg_ ou _/etc/default/grub_) podem conter os parâmetros `rd.lvm.lv` ou `rd_LVM_LV` . Esses parâmetros identificam os dispositivos LVM (Gerenciador de volumes lógicos) que serão descobertos no momento da inicialização. Se esses dispositivos LVM não existirem, o próprio sistema protegido não será inicializado e ficará preso no processo de inicialização. O mesmo problema também será visto com a VM de failover. Veja alguns exemplos:

- Arquivo: _/boot/Grub2/grub.cfg_ em RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Arquivo: _/etc/default/grub_ em RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Arquivo: _/boot/grub/menu.lst_ em RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Em cada exemplo, o GRUB precisa detectar dois dispositivos LVM com os nomes `root` e `swap` do grupo de volumes `rootvg` .

### <a name="fix-the-problem"></a>Corrigir o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos arquivos de configuração do GRUB. Em seguida, tente novamente habilitar a proteção.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Atualização do serviço de mobilidade concluída com avisos (código de erro 151083)

O serviço de mobilidade Site Recovery tem muitos componentes, um dos quais é chamado de driver de filtro. O driver de filtro é carregado na memória do sistema somente durante a reinicialização do sistema. Sempre que uma atualização do serviço de mobilidade inclui alterações de driver de filtro, o computador é atualizado, mas você ainda vê um aviso de que algumas correções exigem uma reinicialização. O aviso é exibido porque as correções do driver de filtro só podem entrar em vigor quando o novo driver de filtro é carregado, o que ocorre apenas durante uma reinicialização.

> [!NOTE]
> Isso é apenas um aviso. A replicação existente continuará funcionando mesmo após a nova atualização do agente. Você pode optar por reiniciar sempre que desejar os benefícios do novo driver de filtro, mas o driver de filtro antigo continuará funcionando se você não reiniciar.
>
> Além do driver de filtro, os benefícios de quaisquer outros aprimoramentos e correções na atualização do serviço de mobilidade entram em vigor sem a necessidade de uma reinicialização.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Proteção não habilitada se o disco gerenciado de réplica existir

Esse erro ocorre quando o disco gerenciado de réplica já existe, sem as marcas esperadas, no grupo de recursos de destino.

### <a name="possible-cause"></a>Causa possível

Esse problema pode ocorrer se a máquina virtual já tiver sido protegida anteriormente e quando a replicação tiver sido desabilitada, o disco de réplica não foi removido.

### <a name="fix-the-problem"></a>Corrigir o problema

Exclua o disco de réplica identificado na mensagem de erro e repita o trabalho de proteção com falha.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Falha ao habilitar a proteção, pois o instalador não pode localizar o disco raiz (código de erro 151137)

Esse erro ocorre em computadores Linux em que o disco do sistema operacional é criptografado usando Azure Disk Encryption (ADE). Este é um problema válido somente na versão 9,35 do agente.

### <a name="possible-causes"></a>Possíveis causas

O instalador não pode localizar o disco raiz que hospeda o sistema de arquivos raiz.

### <a name="fix-the-problem"></a>Corrigir o problema

Siga as etapas abaixo para corrigir esse problema-

1. Localize os bits de agente no diretório _/var/lib/waagent_ em máquinas RHEL e CentOS usando o comando abaixo: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Saída esperada:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Crie um novo diretório e altere o diretório para esse novo diretório.
3. Extraia o arquivo do agente encontrado na primeira etapa aqui, usando o comando abaixo:

    `tar -xf <Tar Ball File>`

4. Abra o arquivo _prereq_check_installer.js_ e exclua as linhas a seguir. Salve o arquivo depois disso.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Invoque o instalador usando o comando: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Se o instalador for executado com sucesso, repita o trabalho habilitar replicação.

## <a name="next-steps"></a>Próximas etapas

[Replicar VMs do Azure para outra região do Azure](azure-to-azure-how-to-enable-replication.md)