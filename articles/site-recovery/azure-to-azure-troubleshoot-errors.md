---
title: Solução de problemas do Azure Site Recovery para erros e assuntos de replicação do Azure para Azure | Microsoft Docs
description: Solucionando problemas de erros e problemas durante a replicação de máquinas virtuais do Azure para recuperação de desastres
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 1e0450554597d99aa99d6df51f22bfc90c0d92ad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798579"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Solucionar problemas de replicação de VM do Azure para o Azure

Este artigo descreve os problemas comuns no Azure Site Recovery na replicação e na recuperação de máquinas virtuais do Azure de uma região para outra região e explica como solucioná-los. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Lista de erros
- **[Problemas de cota de recursos do Azure (código de erro 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Certificados raiz confiáveis (código de erro 151066)](#trusted-root-certificates-error-code-151066)**
- **[Conectividade de saída para o Site Recovery (código de erro 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)
Sua assinatura deve ser habilitada para criar VMs do Azure na região de destino que você planeja usar como a região de recuperação de desastres. Além disso, sua assinatura deve ter uma cota suficiente habilitada para criar VMs de tamanho específico. Por padrão, a recuperação de Site escolhe o mesmo tamanho para a VM de destino como a VM de origem. Se o tamanho correspondente não estiver disponível, o tamanho mais próximo possível é escolhido automaticamente. Se não houver nenhum tamanho correspondente que dá suporte à configuração de VM de origem, essa mensagem de erro será exibida:

**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
150097<br></br>**Mensagem**: Não foi possível habilitar a replicação para a máquina virtual VmName. | -Sua ID de assinatura não deve estar habilitada para criar quaisquer máquinas virtuais no local de região de destino.</br></br>-Sua ID de assinatura não deve estar habilitada ou não tem uma cota suficiente para criar tamanhos específicos de VM no local de região de destino.</br></br>-Um destino adequado tamanho da VM que corresponda a contagem de NIC (2) de VM de origem não foi encontrado para a ID da assinatura no local de região de destino.| Entre em contato com [suporte à cobrança do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para habilitar a criação de VM para os tamanhos VM necessários no local de destino para a sua assinatura. Depois que ele é habilitado, repita a operação falhada.

### <a name="fix-the-problem"></a>Corrija o problema
Entre em contato com [suporte à cobrança do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para habilitar a criação de VM para os tamanhos VM necessários no local de destino para a sua assinatura.

Se o local de destino tiver uma restrição de capacidade, desabilite a replicação e habilite para um local diferente em que sua assinatura tem uma cota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados raiz confiáveis (código de erro 151066)

Se todos os certificados de raiz confiável mais recentes não estiverem presentes na VM, o seu trabalho "habilitar replicação" pode falhar. Sem os certificados, a autenticação e autorização de chamadas de serviço de recuperação de Site da VM falham. A mensagem de erro para o trabalho de recuperação de Site com falha em "habilitar replicação" será exibida:

**Código de erro** | **Possível causa** | **Recomendações**
--- | --- | ---
151066<br></br>**Mensagem**: Falha na configuração do Site Recovery. | Os certificados de raiz confiável obrigatórios usados para a autorização e autenticação não estão presentes no computador. | -Para uma VM que executa o sistema operacional Windows, certifique-se de que os certificados de raiz confiável estão presentes no computador. Para obter informações, consulte [Configurar raízes confiáveis e certificados não permitidos](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Para uma VM que executa o sistema operacional Linux, siga as orientações para certificados de raiz confiável publicadas pelo distribuidor de versão do sistema operacional Linux.

### <a name="fix-the-problem"></a>Corrija o problema
**Windows**

Instale todas as atualizações mais recentes do Windows na máquina virtual para que todos os certificados de raiz confiável estejam presentes no computador. Se você estiver em um ambiente desconectado, siga o processo de atualização padrão do Windows em sua organização para obter os certificados. Se os certificados necessários não estiverem presentes na VM, as chamadas para o serviço de recuperação de Site falharão por razões de segurança.

Siga o típico gerenciamento de atualização do Windows ou o certificado de processo de gerenciamento de atualização na sua organização para conseguir todos os certificados de raiz mais recentes e a lista de certificados revogados atualizada nas VMs.

Para verificar se o problema estiver resolvido, vá para login.microsoftonline.com em um navegador em sua VM.

**Linux**

Siga a recomendação fornecida pelo seu distribuidor Linux para conseguir os certificados de raiz mais recentes e a lista de certificados revogados atualizada na VM.

Como o SuSE Linux usa links simbólicos para manter uma lista de certificados, siga estes passos:

1.  Entre como um usuário raiz.

2.  Execute este comando para alterar o diretório.

      ``# cd /etc/ssl/certs``

1. Verifique se o certificado de autoridade de certificação raiz da Symantec está presente.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Se o certificado da autoridade de certificação raiz da Symantec não for encontrado, execute o comando a seguir para baixar o arquivo. Verifique se há algum erro e siga a ação recomendada para falhas de rede.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Verifique se o certificado de autoridade de certificação raiz da Baltimore está presente.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Se o certificado de autoridade de certificação raiz da Baltimore não for encontrado, baixe-o.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Verifique se o certificado DigiCert_Global_Root_CA está presente.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Se o DigiCert_Global_Root_CA não for encontrado, execute os comandos a seguir para baixar o certificado.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Execute o script de rehash para atualizar os hashes de entidade de certificado para os certificados baixados recentemente.

    ``# c_rehash``

8.  Verifique se a entidade realiza hashes conforme symlinks são criados para os certificados.

    - Comando

      ``# ls -l | grep Baltimore``

    - Saída

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Comando

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Comando

      ``# ls -l | grep DigiCert_Global_Root``

    - Saída

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Criar uma cópia do arquivo VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem com o nome de arquivo b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Criar uma cópia do arquivo Baltimore_CyberTrust_Root.pem com nome de arquivo 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Criar uma cópia do arquivo DigiCert_Global_Root_CA.pem com nome de arquivo 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Verifique se os arquivos estão presentes.  

    - Comando

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema nº 1: Falha ao registrar a máquina virtual do Azure no Site Recovery (151195) </br>
- **Possível causa** </br>
  - Não é possível estabelecer Conexão com pontos de extremidade do Site Recovery devido a falha na resolução DNS.
  - Isso é visto com mais frequência durante a proteção novamente quando você fizer failover da máquina virtual, mas o servidor DNS não está acessível da região de recuperação de Desastre.

- **Resolução**
   - Se você estiver usando o DNS personalizado, certifique-se de que o servidor DNS está acessível da região de recuperação de desastres. Para verificar se você tem um DNS personalizado vá para a VM > rede de Recuperação de Desastres > servidores DNS. Tente acessar o servidor DNS da máquina virtual. Se não estiver acessível, em seguida, disponibilize-o failover do servidor DNS ou crie a linha de site entre a rede de recuperação de Desastre e DNS.

    ![com-erro](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: Falha na configuração do Site Recovery (151196)
- **Possível causa** </br>
  - Não é possível estabelecer a Conexão para pontos de extremidade de IP4 de identidade e autenticação do Office 365.

- **Resolução**
  - O Azure Site Recovery precisa de acesso aos intervalos de IPs do Office 365 para autenticação.
    Se você estiver usando o proxy de regras de firewall NSG (grupo) de segurança de rede do Azure para controlar a conectividade de rede de saída na VM, verifique se você permitiu a comunicação com intervalos de IP do O365. Criar uma [marca de serviço do Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) com base em regra NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
      - Se novos endereços são adicionados no futuro para o Azure Active Directory (AAD) você precisará criar novas regras NSG.

> [!NOTE]
> Se as máquinas virtuais estão por trás **Standard** balanceador de carga interno e em seguida, ele não teria acesso ao O365 IPs, ou seja, login.microsoftonline.com por padrão. Altere-o para **básicas** interno tipo de Balanceador de carga ou criar acesso com associação de saída, conforme mencionado na [artigo](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: Falha na configuração do Site Recovery (151197)
- **Possível causa** </br>
  - Não é possível estabelecer conexão com pontos de extremidade de serviço de Recuperação de Site.

- **Resolução**
  - O Azure Site Recovery requer acesso para a [intervalos de IP de recuperação de Site](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) dependendo da região. Certifique-se de que necessários intervalos os de IP são acessíveis a partir da máquina virtual.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: A2A a replicação falha quando o tráfego de rede passará por meio do servidor de proxy no local (151072)
- **Possível causa** </br>
  - As configurações de proxy personalizadas são inválidas e agente de serviço de mobilidade do Azure Site Recovery detectar automaticamente as configurações de proxy do IE


- **Resolução**
  1. O agente do Serviço de Mobilidade detecta as configurações de proxy do IE no Windows e o ambiente /etc/ no Linux.
  2. Se você preferir definir proxy somente para o serviço de mobilidade do Azure Site Recovery, você pode fornecer os detalhes de proxy no ProxyInfo.conf localizado em:</br>
     - ``/usr/local/InMage/config/`` no ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` no ***Windows***
  3. O ProxyInfo.conf deve ter as configurações de proxy no seguinte formato INI.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Agente de serviço de mobilidade de recuperação de Site do Azure dá suporte a apenas ***proxies utenticados***.


### <a name="fix-the-problem"></a>Corrija o problema
Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou o [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as etapas a [documento de diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco não encontrado na máquina (código de erro 150039)

Um novo disco anexado à máquina virtual deve ser inicializado.

**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
150039<br></br>**Mensagem**: O disco de dados do Azure (DiskName) (DiskURI) com o LUN (número de unidade lógica) (LUNValue) não foi mapeado para um disco correspondente que está sendo relatado na VM que tem o mesmo valor de LUN. | - Um novo disco de dados foi anexado à VM, mas ele não foi inicializado.</br></br>- O disco de dados na máquina virtual não está relatando corretamente o valor do LUN no qual o disco foi conectado à máquina virtual.| Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação.</br></br>Para Windows: [Anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Para Linux: [Inicializar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Corrija o problema
Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação:

- Para Windows: [Anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Para Linux: [Adicionar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se o problema persistir, contate o Suporte.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Um ou mais discos estão disponíveis para proteção (código de erro 153039)
- **Possível causa** </br>
  - Se um ou mais discos adicionados recentemente para a máquina virtual após a proteção. 
  - Se um ou mais discos foram inicializados mais tarde, depois a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrija o problema
Você pode optar por proteger os discos ou ignorar o aviso para tornar o status de replicação da VM íntegra novamente.</br>
1. Para proteger os discos. Navegue até itens replicados > máquina virtual > discos > clique no disco desprotegido > Habilitar replicação.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Para ignorar o aviso. Vá para itens replicados > máquina virtual > clique no alerta ignorar na seção de visão geral.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Remover a máquina virtual do cofre foi concluído com informações (código de erro 150225)
No momento da proteção da máquina virtual, o Azure Site Recovery cria alguns links na máquina virtual de origem. Quando você remove a proteção ou desabilite a replicação, o Azure Site Recovery remover esses vínculos como parte do trabalho de limpeza. No caso da máquina virtual tem um bloqueio de recurso, em seguida, o trabalho seja concluído com as informações. Ele informa que a máquina virtual foi removida do cofre dos serviços de recuperação, mas alguns dos links obsoletos não foi possível limpar da máquina de origem.

Você pode ignorar este aviso caso não pretenda proteger essa máquina virtual novamente no futuro. No entanto, se você tem para proteger essa máquina virtual mais tarde, em seguida, você deve limpar os links conforme mencionado nas etapas abaixo. 

**Se você não fizer a limpeza para cima, em seguida:**

1.  Durante o tempo de habilitar a replicação por meio do Cofre de serviços de recuperação, a máquina virtual não será listada. 
2.  Se você tentar proteger a VM por meio **Máquina Virtual > Configurações > recuperação de desastres** ele será falhou com o erro "*não é possível habilitar a replicação devido aos links de recursos obsoletos existentes na VM*".


### <a name="fix-the-problem"></a>Corrija o problema

>[!NOTE]
>
>O Azure Site Recovery não excluir a máquina virtual de origem ou impacto que ela de qualquer forma ao executar as etapas a seguir.
>

1. Remover o bloqueio da VM ou grupo de recursos. Por exemplo:  Abaixo VM nome "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover configuração obsoleta do Azure Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Forneça o nome VM e ID, VM do grupo de recursos de assinatura como um parâmetro.
5. Se for solicitado a credenciais do Azure, forneça que e verificar que o script é executado sem falhas. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Não é possível habilitar a replicação devido aos links de recursos obsoletos existentes na VM (código de erro 150226)

**Causa: Máquina virtual tem configuração obsoleta deixada da proteção de recuperação de Site anterior**

A configuração obsoleta pode ser deixada em uma VM do Azure nos seguintes casos:

- Habilitar a replicação para a VM do Azure usando o Site Recovery e, em seguida, desabilite a replicação, mas o **VM de origem tinha um bloqueio de recurso**.
- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.
- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no grupo de recursos contendo o cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.

### <a name="fix-the-problem"></a>Corrija o problema

>[!NOTE]
>
>O Azure Site Recovery não excluir a máquina virtual de origem ou impacto que ela de qualquer forma ao executar as etapas a seguir.


1. Remover o bloqueio da VM ou grupo de recursos, caso haja algum. *Por exemplo:* Abaixo VM nome "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover configuração obsoleta do Azure Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Forneça o nome VM e ID, VM do grupo de recursos de assinatura como um parâmetro.
5. Se for solicitado a credenciais do Azure, forneça que e verificar que o script é executado sem falhas.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Não é possível ver o VM do Azure ou grupo de recursos para a seleção em "habilitar replicação"

 **Causa 1:  O grupo de recursos e a Máquina Virtual de origem estão em uma localização diferente**
 
O Azure Site Recovery atualmente normas que o grupo de recursos de região e as máquinas virtuais de origem devem estar no mesmo local. Se não for o caso, em seguida, você não seria capaz de encontrar a máquina virtual ou um grupo de recursos durante o tempo de proteção. 

**Como uma solução alternativa**, você pode habilitar a replicação da VM, em vez do cofre dos serviços de recuperação. Vá para a VM de origem > Propriedades > Habilitar a replicação e recuperação de desastres.

**Causa 2: O grupo de recursos não faz parte da assinatura selecionada**

Talvez não seja possível localizar o grupo de recursos no momento da proteção, se ele não fizer parte da assinatura fornecida. Certifique-se de que o grupo de recursos pertence à assinatura que está sendo usada.

 **Causa 3: Configuração obsoleta**
 
Se VM que você deseja habilitar para replicação não é exibida, pode haver uma configuração obsoleta do Site Recovery na VM do Azure. A configuração obsoleta pode ser deixada em uma VM do Azure nos seguintes casos:

- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.
- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no grupo de recursos contendo o cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.

- Habilitar a replicação para a VM do Azure usando o Site Recovery e, em seguida, desabilite a replicação, mas a VM de origem tinha um bloqueio de recurso.

### <a name="fix-the-problem"></a>Corrija o problema

> [!NOTE]
>
> Atualize o módulo ""AzureRM.Resources"" antes de usar o script abaixo. O Azure Site Recovery não excluir a máquina virtual de origem ou impacto que ela de qualquer forma ao executar as etapas a seguir.
>

1. Remover o bloqueio da VM ou grupo de recursos, caso haja algum. *Por exemplo:* Abaixo VM nome "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover a configuração obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Forneça o nome VM e ID, VM do grupo de recursos de assinatura como um parâmetro.
5. Se for solicitado a credenciais do Azure, forneça que e verificar que o script é executado sem falhas.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Não é possível selecionar a máquina virtual para proteção
 **Causa 1:  A máquina virtual tem uma extensão instalada em um estado com falha ou sem resposta** <br>
 Vá para Máquinas virtuais > Configuração > Extensões e verifique se há extensões em um estado com falha. Desinstale a extensão com falha e tente proteger a máquina virtual novamente.<br>
 **Causa 2:  [O estado de provisionamento da VM não é válido](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para habilitar a replicação na VM, o estado de provisionamento deve ser **Com êxito**. Você pode verificar o estado da VM seguindo as etapas abaixo.

1.  Selecione o **Resource Explorer** de **Todos os Serviços** no Portal do Azure.
2.  Expanda a lista **Assinaturas** e selecione sua assinatura.
3.  Expanda a lista **ResourceGroups** e selecione o grupo de recursos da VM.
4.  Expanda a lista **Recursos** e selecione sua máquina virtual
5.  Marque o campo **provisioningState** na exibição Instância, no lado direito.

### <a name="fix-the-problem"></a>Corrija o problema

- Se **provisioningState** estiver com **Falha**, entre em contato com o suporte com detalhes para solucionar o problema.
- Se **provisioningState** for **Atualizando**, outra extensão poderá estar sendo implantada. Verifique se há outras operações em andamento na VM, aguarde a conclusão e tente realizar novamente o trabalho **Habilitar replicação** do Site Recovery.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Não é possível selecionar a rede virtual de destino - a guia de seleção de rede está esmaecida.

**Causa 1: Se a VM estiver anexada a uma rede que já esteja mapeada para uma 'Rede de destino'.**
- Se a VM de origem fizer parte de uma rede virtual e outra VM da mesma rede virtual já estiver mapeada com uma rede no grupo de recursos de destino, em seguida, por padrão, o menu suspenso de seleção de rede padrão será desabilitado.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Causa 2: Se, anteriormente, você protegeu a VM usando o Azure Site Recovery e desabilitou a replicação.**
 - Desabilitar a replicação de uma VM não exclui o Mapeamento de Rede. Ele deve ser excluído do cofre do serviço de recuperação em que a VM foi protegida. </br>
 Navegue até o cofre do serviço de recuperação > Infraestrutura do Site Recovery > Mapeamento de rede. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - A rede de destino configurada durante a configuração da recuperação de desastre pode ser alterada após a configuração inicial, depois que a VM estiver protegida. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Observe que alterar o mapeamento de rede afeta todas as VMs protegidas que usam o mapeamento de rede específico.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>Erro de serviço de Cópias de Sombra de Volume/COM+ (código de erro 151025)

**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
151025<br></br>**Mensagem**: Extensão do site Recovery não conseguiu instalar | - Serviço 'Aplicativo do Sistema COM' desabilitado.</br></br>- O serviço de 'Cópias de Sombra de Volume' está desabilitado.| Defina os serviços 'Aplicativo do Sistema COM+' e 'Cópias de Sombra de Volume' para o modo de inicialização manual ou automático.

### <a name="fix-the-problem"></a>Corrija o problema

É possível abrir o console de 'Serviços' e assegurar que 'Aplicativo do Sistema COM+' e 'Cópias de Sombra de Volume' não estejam definidos como 'Desabilitado' para 'Tipo de Inicialização'.
  ![erro-com](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho do disco gerenciado não suportado (código de erro 150172)


**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
150172<br></br>**Mensagem**: Não foi possível habilitar a proteção para a máquina virtual pois ela tem (DiskName) com tamanho (DiskSize) menor do que o mínimo suportado de 1024 MB. | -O disco é menor que o tamanho suportado de 1024 MB| Certifique-se de que os tamanhos de disco estão dentro do limite de tamanho suportado e repita a operação.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A habilitação da proteção falhou porque o nome do dispositivo foi mencionado na configuração do GRUB em vez do UUID (código de erro 151126)

**Possível causa:** </br>
Os arquivos de configuração do GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" ou "/etc/default/grub") podem conter o valor dos parâmetros **root** e **resume** como os nomes de dispositivo reais em vez do UUID. O Site Recovery exige a abordagem com UUID, pois o nome dos dispositivos poderá ser alterado quando ocorrer uma reinicialização da VM, uma vez que a VM poderá não ter o mesmo nome após um failover, causando problemas. Por exemplo: </br>


- A seguinte linha é do arquivo GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- A seguinte linha é do arquivo GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Se você observar a cadeia de caracteres em negrito acima, o GRUB terá nomes de dispositivo reais para os parâmetros "root" e "resume" em vez do UUID.

**Como corrigir:**<br>
os nomes de dispositivo devem ser substituídos pelo UUID correspondente.<br>


1. Localizar o UUID do dispositivo executando o comando "blkid \<nome do dispositivo >". Por exemplo:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site Recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  Site Recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
