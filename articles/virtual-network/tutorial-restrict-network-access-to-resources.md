---
title: Restringir o acesso de recursos de PaaS – tutorial – portal do Azure
description: Neste tutorial, você aprende a limitar e restringir o acesso à rede de recursos do Azure, como Armazenamento do Azure e Banco de Dados SQL do Azure, com pontos de extremidade de serviço de rede virtual usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: 22494d292077f4b6018a4512b45b5fe2caa9c8ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057194"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutorial: restringir o acesso à rede de recursos de PaaS com pontos de extremidade de serviço de rede virtual usando o Portal do Azure

Os pontos de extremidade de serviço de rede virtual permitem limitar o acesso à rede a alguns recursos de serviço do Azure para uma sub-rede da rede virtual. Você também pode remover o acesso à Internet para os recursos. Os pontos de extremidade de serviço fornecerão conexão direta de sua rede virtual a um serviço do Azure, permitindo que você use o espaço de endereço privado da sua rede virtual para acessar os serviços do Azure compatíveis. O tráfego destinado aos recursos do Azure por meio de pontos de extremidade de serviço sempre fica na rede de backbone do Microsoft Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual com uma sub-rede
> * Adicionar uma sub-rede e habilitar um ponto de extremidade de serviço
> * Criar um recurso do Azure e permitir o acesso à rede para ele apenas de uma sub-rede
> * Implantar uma VM (máquina virtual) para cada sub-rede
> * Confirmar o acesso a um recurso por meio de uma sub-rede
> * Confirmar se o acesso é negado para um recurso por meio de uma sub-rede e da Internet

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-restrict-network-access-to-resources-cli.md) ou o [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e **Redes virtuais**.
3. Clique em **+ Adicionar** e insira as seguintes informações: 

   |Configuração|Valor|
   |----|----|
   |Subscription| Selecionar sua assinatura|
   |Resource group | Selecione **Criar novo** e insira *myResourceGroup*.|
   |Nome| Insira *myVirtualNetwork* |
   |Região| Selecione **(EUA) Leste dos EUA** |

   ![Insira s informações básicas sobre sua rede virtual](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Clique em **Avançar: Endereços IP >**
   
   |Configuração|Valor|
   |----|----|
   |Espaço de endereço IPv4| Mantenha-o como o padrão |
   |Nome da sub-rede| Clique em **padrão** e altere o nome de "padrão" para "Público"|
   |Intervalo de endereços da sub-rede| Mantenha-o como o padrão|

5. Clique em **Avançar: Segurança >** 
   
   |Configuração|Valor|
   |----|----|   
   |BastionHost| Desabilitar|
   |Proteção contra DDOS| Desabilitar|
   |Firewall| Desabilitar|

6. Ao concluir, clique em **Examinar e criar**.
7. Se as verificações de validação forem aprovadas, clique em **Criar**.
8. Aguarde a conclusão da implantação e clique em **Ir para recurso** ou prossiga para a próxima seção. 

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço

Pontos de extremidade de serviço são habilitados por serviço, por sub-rede. Para criar uma sub-rede e habilitar um ponto de extremidade de serviço para ela:

1. Se você ainda não estiver na página de recursos de rede virtual, pesquise a rede recém-criada na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, insira *myVirtualNetwork* e selecione-a na lista.
2. No menu **Configurações** (à esquerda), selecione **Sub-redes** e **+ Sub-rede**, conforme mostrado:

    ![Adicionar sub-rede](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Em **Adicionar sub-rede**, selecione ou insira as informações a seguir e selecione **OK**:

    |Configuração|Valor|
    |----|----|
    |Nome| Privado |
    |Intervalo de endereços| Mantenha-o como o padrão|
    |Pontos de extremidade de serviço| Selecione **Microsoft.Storage**|
    |Políticas do ponto de extremidade de serviço | Mantenha o padrão como 0 |

> [!CAUTION]
> Antes de habilitar um ponto de extremidade de serviço para uma sub-rede existente que tenha recursos nela, consulte [Alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

4. Clique em **Salvar** e feche a janela Sub-rede à direita. A sub-rede recém-criada será exibida na lista.

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso à rede de uma sub-rede

Por padrão, todas as instâncias de máquina virtual em uma sub-rede podem se comunicar com todos os recursos. Você pode limitar a comunicação com todos os recursos em uma sub-rede criando um grupo de segurança de rede e associando-o à sub-rede:

1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure.
2. Escolha **Rede** e selecione (ou procure) **Grupos de segurança de rede**.
3. Na página **Grupos de segurança de rede**, clique em **+ Adicionar**.
4. Inserir as seguintes informações 

    |Configuração|Valor|
    |----|----|
    |Subscription| Selecionar sua assinatura|
    |Grupo de recursos | Selecione *myResourceGroup* na lista|
    |Nome| Insira **myNsgPrivate** |
    |Location| Selecione **Leste dos EUA** |

5. Clique em **Examinar + criar** e, quando a verificação de validação for aprovada, clique em **Criar**.
6. Depois que o grupo de segurança de rede for criado, clique em **Ir para recurso** ou procure *myNsgPrivate*.
7. Em **Configurações** à esquerda, selecione **Regras de segurança de saída**.
8. Selecione **+ Adicionar**.
9. Crie uma regra que permita a comunicação de saída para o serviço de Armazenamento do Azure. Insira ou selecione as seguintes informações e selecione **Adicionar**:

    |Configuração|Valor|
    |----|----|
    |Fonte| Selecione **VirtualNetwork** |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **Marca de Serviço**|
    |Marca de serviço de destino | Selecione **Armazenamento**|
    |Intervalos de portas de destino| Mantenha o padrão como *8080* |
    |Protocolo|Qualquer|
    |Ação|Allow|
    |Prioridade|100|
    |Nome|Renomeie-o para **Allow-Storage-All**|

10. Crie outra regra de segurança de saída que nega a comunicação com a Internet. Essa regra substitui uma regra padrão em todos os grupos de segurança de rede que permite a comunicação de saída à Internet. Conclua as etapas 6 a 9 acima usando os seguintes valores:

    |Configuração|Valor|
    |----|----|
    |Fonte| Selecione **VirtualNetwork** |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **Marca de Serviço**|
    |Marca de serviço de destino| Selecione **Internet**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|**Altere o padrão para *Negar*** |
    |Prioridade|110|
    |Nome|Altere-o para *Deny-Internet-All*|

11. Crie uma *regra de segurança de entrada* que permita o tráfego do protocolo RDP à sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. Conexões de área de trabalho remota são permitidas para a sub-rede para que a conectividade possa ser testada em uma etapa posterior. 
12. Em **Configurações**, selecione **Regras de segurança de entrada**.
13. Selecione **+ Adicionar** e use os seguintes valores:

    |Configuração|Valor|
    |----|----|
    |Fonte| Qualquer |
    |Intervalos de portas de origem| * |
    |Destino | Selecione **VirtualNetwork**|
    |Intervalos de portas de destino| Altere-o para *3389* |
    |Protocolo|Qualquer|
    |Ação|Allow|
    |Prioridade|120|
    |Nome|Altere-o para *Allow-RDP-All*|

   >[!WARNING] 
   > A porta 3389 RDP é exposta à Internet. Isso só é recomendado para testes. Para *ambientes de produção*, recomendamos usar uma VPN ou uma conexão privada.

1.  Em **Configurações**, selecione **Sub-redes**.
2.  Clique em **+ Associar**.
3.  Em **Rede virtual**, selecione **myVirtualNetwork**.
4.  Em **Sub-rede**, escolha **Privada** e **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso à rede para um recurso

As etapas necessárias para restringir o acesso à rede aos recursos criados por meio dos serviços do Azure, que são habilitados para pontos de extremidade de serviço, variam conforme o serviço. Confira a documentação de serviços individuais para obter as etapas específicas para cada serviço. O restante deste tutorial inclui etapas para restringir o acesso de rede para uma conta de Armazenamento do Microsoft Azure como exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Insira "Conta de armazenamento" na barra de pesquisa e selecione-a no menu suspenso.
3. Clique em **+ Adicionar**.
4. Insira as seguintes informações:

    |Configuração|Valor|
    |----|----|
    |Subscription| Selecionar sua assinatura|
    |Grupo de recursos| Selecione *myResourceGroup*|
    |Nome da Conta de Armazenamento| Insira um nome que seja exclusivo em todos os locais do Azure, com 3 a 24 caracteres de comprimento, usando apenas números e letras minúsculas.|
    |Localização| Selecione **(EUA) Leste dos EUA** |
    |Desempenho|Standard|
    |Tipo de conta| StorageV2 (v2 de uso geral)|
    |Replicação| Armazenamento com redundância local (LRS)|

5. Selecione **Criar + examinar** e, quando as verificações de validação forem aprovadas, clique em **Criar**. 

>[!NOTE] 
> A implantação poderá levar alguns minutos para ser concluída.

6. Depois que a conta de armazenamento for criada, clique em **Ir para recurso**

### <a name="create-a-file-share-in-the-storage-account"></a>Criar um compartilhamento de arquivos na conta de armazenamento

1. Acesse a página de visão geral da conta de armazenamento.
2. Selecione o ícone de aplicativo **Compartilhamentos de Arquivos** e clique em **+ Compartilhamento de arquivo**.

    |Configuração|Valor|
    |----|----|
    |Nome| my-file-share|
    |Quota| 'Definir como máximo' |

   ![Conta de armazenamento](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Clique em **Criar**.
4. O compartilhamento de arquivo será mostrado na janela do Azure; caso contrário, clique em **Atualizar**

### <a name="restrict-network-access-to-a-subnet"></a>Restringir o acesso à rede para uma sub-rede

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede, incluindo a Internet. Você pode restringir o acesso à rede pela Internet e a todas as outras sub-redes em todas as redes virtuais (exceto para a sub-rede *Privada* na rede virtual *myVirtualNetwork*). Para restringir o acesso à rede para uma sub-rede:

1. Em **Configurações** da sua conta de armazenamento (nomeada exclusivamente), selecione **Rede**.
2. Selecione **Redes selecionadas**.
3. Selecione **+ Adicionar rede virtual existente**.
4. Em **Adicionar redes**, selecione os seguintes valores e **Adicionar**:

    |Configuração|Valor|
    |----|----|
    |Subscription| Selecionar sua assinatura|
    |Redes virtuais| **myVirtualNetwork**|
    |Sub-redes| **Privado**|

    ![A captura de tela mostra o painel Adicionar redes em que você pode inserir os valores especificados.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Clique em **Adicionar** e clique imediatamente no ícone **Salvar** para salvar as alterações.
6. Em **Configurações** da conta de armazenamento, selecione **Chaves de acesso**, conforme mostrado na seguinte imagem:

      ![Captura de tela mostra as Chaves de acesso selecionadas nas configurações em que você pode obter uma chave.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Clique em **Mostrar chaves** e anote os valores de **Chave**, pois você precisará inserir a chave 1 manualmente em uma etapa posterior ao mapear o compartilhamento de arquivo para uma letra da unidade em uma VM.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso à rede para uma conta de armazenamento, implante uma VM para cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

1. Na barra "Recursos de pesquisa", . ." procure **Máquinas virtuais**.
2. Selecione **+ Adicionar > Máquinas virtuais**. 
3. Insira as seguintes informações:

   |Configuração|Valor|
   |----|----|
   |Subscription| Selecionar sua assinatura|
   |Grupo de recursos| Selecione **myResourceGroup, que foi criado anteriormente.|
   |Nome da máquina virtual| Insira *myVmPublic*|
   |Região | (EUA) Leste dos EUA
   |Opções de disponibilidade| Zona de disponibilidade|
   |Zona de disponibilidade | 1 |
   |Imagem | Windows Server 2019 Datacenter – Gen 1 |
   |Tamanho | Selecione o tamanho da instância de VM que deseja usar |
   |Nome de Usuário|Insira um nome de usuário de sua escolha.|
   |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Porta de entrada públicas | Permitir as portas selecionadas |
   |Selecione as portas de entrada | Deixe o padrão definido como *RDP (3389)* |

   ![Selecione uma rede virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Escolha a guia **Rede** e selecione **myVirtualNetwork**. 
5. Escolha a sub-rede *Pública*.
6. Em **Grupo de segurança de Rede da NIC**, escolha **Avançado**. O portal cria automaticamente um grupo de segurança de rede que permite a porta 3389, a qual você vai precisará abrir para se conectar à máquina virtual em uma etapa posterior. 

   ![Digite informações básicas sobre uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Escolha **Examinar e criar**, **Criar** e aguarde a conclusão da implantação.
8. Clique em **Ir para recurso** ou abra **Página Inicial > Máquinas virtuais** e selecione a VM recém-criada *myVmPublic*, que deverá ser iniciada.

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

1. Conclua as etapas 1 a 8 novamente, mas na etapa 3, nomeie a máquina virtual *myVmPrivate* e defina **Porta pública de entrada** como "Nenhuma". 
2. Nas etapas 4 e 5, selecione a sub-rede **Privada**.

>[!NOTE]
> As configurações **Grupo de segurança de rede da NIC** e **Portas de entrada públicas** devem espelhar a imagem mostrada abaixo, incluindo a janela de confirmação azul que indica "Todo o tráfego da Internet pública será bloqueado por padrão".

   ![Criar uma máquina virtual privada](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Escolha **Examinar e criar**, **Criar** e aguarde a conclusão da implantação. 

>[!WARNING]
> Não prossiga para a próxima etapa até que a implantação seja concluída.

4. Aguarde a janela de confirmação mostrada abaixo e clique em **Ir para recurso**.

   ![Criar uma janela de confirmação de máquina virtual privada](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Confirmar acesso à conta de armazenamento

1. Depois que a VM *myVmPrivate* for criada, clique em **Ir para recurso**. 
2. Conecte-se à VM selecionando **Conectar > RDP**.
3. Após a seleção do botão **Conectar**, um arquivo .rdp (protocolo RDP) será criado. Clique em **Baixar Arquivo RDP** para baixá-lo no computador.  
4. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais opções** e **Usar outra conta** para especificar as credenciais inseridas durante a criação da VM. No campo de email, insira as credenciais da "Conta de administrador: nome de usuário" especificadas anteriormente. 
5. Selecione **OK**.
6. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão. Você verá a VM ser inicializada conforme mostrado abaixo:

   ![Mostrar a máquina virtual privada em execução](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. Na janela da VM, abra uma instância da CLI do PowerShell.
8. Usando o script abaixo, mapeie o compartilhamento de arquivo do Azure para a unidade Z usando o PowerShell. Antes de executar os comandos a seguir, substitua os campos `<storage-account-key>` e `<storage-account-name>` pelos valores fornecidos anteriormente em [Criar uma conta de armazenamento](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   O PowerShell retorna uma saída semelhante à seguinte saída de exemplo:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   O compartilhamento de arquivos do Azure foi mapeado com êxito para a unidade Z.

9.   Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso é negado para a conta de armazenamento

1. Digite *myVmPublic* na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal.
2. Quando **myVmPublic** for exibido nos resultados da pesquisa, selecione-o.
3. Conclua as etapas 1 a 8 acima em [Confirmar acesso à conta de armazenamento](#confirm-access-to-storage-account) para a VM *myVmPublic*.

   Após uma breve espera, você verá um erro `New-PSDrive : Access is denied`. O acesso é negado porque a VM *myVmPublic* é implantada na sub-rede *Pública*. A sub-rede *Pública* não tem um ponto de extremidade de serviço habilitado para Armazenamento do Azure. A conta de armazenamento só permite o acesso à rede a partir da sub-rede *Privada*, não da sub-rede *Pública*.

4. Feche a sessão da área de trabalho remota para a VM *myVmPublic*.
5. De volta ao portal do Azure, acesse a conta de armazenamento nomeada exclusivamente que você já criou.
6. Em Serviço de Arquivo, selecione **Compartilhamentos de arquivos**, o *my-file-share* criado anteriormente.
7. Você deve receber a seguinte mensagem de erro:

   ![Erro de acesso negado](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> Acesso negado porque o computador não está na sub-rede *Privada* da rede virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou um ponto de extremidade de serviço para uma sub-rede de rede virtual. Você aprendeu que pode habilitar pontos de extremidade de serviço para recursos implantados a partir de vários serviços do Azure. Você criou uma conta de Armazenamento do Azure e acesso restrito à rede para a conta de armazenamento apenas para os recursos em uma sub-rede de rede virtual. Para saber mais sobre pontos de extremidade de serviços, consulte [Visão geral de pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md) e [Gerenciar sub-redes](virtual-network-manage-subnet.md).

Se você tem várias redes virtuais na conta, convém conectar duas redes virtuais em conjunto para que os recursos de cada rede virtual possam se comunicar uns com os outros. Para saber mais sobre como conectar redes virtuais, siga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Conectar redes virtuais](./tutorial-connect-virtual-networks-portal.md)
