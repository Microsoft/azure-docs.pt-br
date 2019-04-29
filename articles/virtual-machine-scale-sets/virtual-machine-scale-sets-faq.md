---
title: Perguntas frequentes sobre os conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Obtenha respostas para as perguntas frequentes sobre os conjuntos de dimensionamento de máquinas virtuais.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 56a31770c374cdccaec4dbee751925a6da00fa59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620236"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Perguntas frequentes sobre os conjuntos de dimensionamento de máquinas virtuais do Azure

Obtenha respostas para as perguntas frequentes sobre os conjuntos de dimensionamento de máquinas virtuais no Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Perguntas frequentes sobre os conjuntos de dimensionamento

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Quantas VMs posso ter em um conjunto de dimensionamento?

Um conjunto de dimensionamento pode ter de 0 a 1.000 VMs baseadas em imagens da plataforma ou de 0 a 600 VMs baseadas em imagens personalizadas.

### <a name="are-data-disks-supported-within-scale-sets"></a>Há suporte para os discos de dados nos conjuntos de dimensionamento?

Sim. Um conjunto de dimensionamento pode definir uma configuração de discos de dados anexados que se aplica a todas as máquinas virtuais no conjunto. Para saber mais, confira [Conjuntos de dimensionamento do Azure e discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). Outras opções para armazenamento de dados incluem:

* Arquivos do Azure (unidades compartilhada de SMB)
* Unidade do sistema operacional
* Unidade TEMP (local, não tem relação com o Armazenamento do Azure)
* Serviço de dados do Azure (por exemplo, Tabelas e Blobs do Azure)
* Serviço de dados externo (por exemplo, banco de dados remoto)

### <a name="which-azure-regions-support-scale-sets"></a>Quais são as regiões do Azure que dão suporte aos conjuntos de dimensionamento?

Todas as regiões dão suporte aos conjuntos de dimensionamento.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Como posso criar um conjunto de dimensionamento usando uma imagem personalizada?

Criar e capturar uma imagem de VM e depois usá-la como a fonte para o conjunto de dimensionamento. Para obter um tutorial de como criar e usar uma imagem de VM personalizada, use a [CLI do Azure](tutorial-use-custom-image-cli.md) ou o [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Se eu reduzir a capacidade do meu conjunto de dimensionamento de 20 para 15, quais VMs serão removidas?

Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade. VMs com as IDs mais altas são removidas primeiro.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>E se eu aumentar a capacidade de 15 para 18?

Se você aumentar a capacidade para 18, serão criadas três novas VMs. Em casa caso, a ID da instância VM será incrementada do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre domínios de falha e domínios de atualização.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Ao usar várias extensões em um conjunto de dimensionamento, posso impor uma sequência de execução?

Sim, você pode usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md) em conjuntos de dimensionamento.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

Um conjunto de dimensionamento regional (não zonal) usa *grupos de veiculações*, que agem como um conjunto de disponibilidade implícito com cinco domínios de falha e cinco domínios de atualização. Conjuntos de dimensionamento de mais de 100 VMs abrangem vários grupos de posicionamento. Para saber mais sobre grupos de posicionamento, confira [Como trabalhar com conjuntos de dimensionamento grandes de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de máquinas virtuais pode existir na mesma rede virtual como um conjunto de escala de VMs. Uma configuração comum é colocar as VMs que geralmente exigem configuração exclusiva no conjunto de disponibilidade do nó de controle e colocar nós de dados no conjunto de dimensionamento.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Os conjuntos de dimensionamento funcionam com as zonas de disponibilidade do Azure?

 Sim! Para obter mais informações, confira a [documentação de zona de conjunto de dimensionamento](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as práticas recomendadas para o Dimensionamento Automático do Azure?

Para obter as práticas recomendadas para o Dimensionamento Automático, consulte as [Práticas recomendadas para o dimensionamento automático das máquinas virtuais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Onde localizar os nomes de métrica para o dimensionamento automático usando as métricas baseadas em host?

Para ver os nomes de métrica para o dimensionamento automático que usa as medidas baseadas em host, consulte o [Suporte para as métricas com o Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Há exemplos de dimensionamento automático baseado em um tópico de Azure Service Bus e comprimento da fila?

Sim. Para obter exemplos de dimensionamento automático baseado em um tópico de Azure Service Bus e comprimento de fila, consulte as [Métricas comuns do dimensionamento automático do Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Para ver uma fila de Barramento de Serviço, use o seguinte JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para ver uma fila de armazenamento, use o seguinte JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Substitua os valores de exemplo pelos URIs (Uniform Resource Identifier) do recurso.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Devo dimensionar automaticamente usando as métricas baseadas em host ou uma extensão de diagnóstico?

Você pode criar uma configuração de dimensionamento automático em uma VM para usar as métricas no nível do host ou as métricas baseadas no SO convidado.

Para obter uma lista das métricas com suporte, consulte [Métricas comuns de dimensionamento automático do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Para obter um exemplo completo dos conjuntos de dimensionamento de máquinas virtuais, consulte a [Configuração avançada do dimensionamento automático usando modelos do Resource Manager para os conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

O exemplo usa a métrica da CPU no nível do host e uma métrica de contagem de mensagens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Como defino as regras de alerta em um conjunto de dimensionamento de máquinas virtuais?

Você pode criar alertas nas métricas dos conjuntos de dimensionamento de máquinas virtuais via PowerShell ou CLI do Azure. Para obter mais informações, consulte [exemplos de início rápido do PowerShell do Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) e [exemplos de início rápido da CLI de plataforma cruzada do Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

A TargetResourceId do conjunto de dimensionamento de máquinas virtuais tem esta aparência:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Você pode escolher qualquer contador de desempenho da VM como a métrica para a qual definir um alerta. Para obter mais informações, consulte as [Métricas do SO convidado para as VMs do Windows baseadas no Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) e as [Métricas do SO convidado para as VMs do Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) no artigo [Métricas comuns de dimensionamento automático do Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Como configuro o dimensionamento automático em um conjunto de dimensionamento de máquinas virtuais usando o PowerShell?

Para configurar o dimensionamento automático em um conjunto de dimensionamento de máquinas virtuais usando o PowerShell, confira [dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais](tutorial-autoscale-powershell.md). Você também pode configurar o dimensionamento automático com a [CLI do Azure](tutorial-autoscale-cli.md) e os [modelos do Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Se eu tiver interrompido (desalocado) uma VM, essa VM será iniciada como parte de uma operação de dimensionamento automático?

 Não. Se as regras de dimensionamento automático exigirem outras instâncias de VM como parte de um conjunto de dimensionamento, uma nova instância de VM será criada. Instâncias VM que são interrompidas (desalocadas) não são iniciadas como parte de um evento de dimensionamento automático. No entanto, essas VMs interrompidas (desalocadas) poderão ser excluídas como parte de um evento de dimensionamento automático que aumenta o número de instâncias, da mesma maneira que qualquer instância de VM pode ser excluída com base na ordem de ID da instância de VM.



## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Como envio um certificado para a VM com segurança? Como provisiono um conjunto de dimensionamento de máquinas virtuais para executar um site no qual o SSL para o site é fornecido com segurança a partir de uma configuração do certificado? (A operação de rotação comum do certificado seria quase igual a uma operação de atualização da configuração.) Você tem um exemplo de como fazer isso?

Para enviar com segurança um certificado para a VM, você pode instalar um certificado de cliente diretamente em um armazenamento de certificados do Windows a partir do cofre de chaves do cliente.

Use o JSON a seguir:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

O código suporta o Windows e o Linux.

Para obter mais informações, veja [Criar ou atualizar um conjunto de dimensionamento de máquinas virtuais](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Exemplo de certificados autoassinados provisionados para Clusters do Azure Service Fabric.
Para ver o exemplo mais recente, use a seguinte instrução da CLI do Azure dentro do shell do Azure e leia a documentação de exemplo do módulo da CLI do Service Fabric, a qual será impressa para stdout:

```bash
az sf cluster create -h
```

Examine a documentação de keyvaults para as operações de certificado com suporte da API mais recente no Azure.

Os certificados autoassinados não podem ser usados para confiança distribuída fornecida por uma Autoridade de Certificação e não devem ser usados para qualquer Cluster do Service Fabric destinado a hospedar soluções de produção empresarial; para obter diretrizes adicionais do Service Fabric Security, examine [Melhores Práticas de Segurança do Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices) e [Cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Posso especificar um par de chaves SSH para usar para a autenticação do SSH com um conjunto de dimensionamento de máquinas virtuais do Linux a partir de um modelo do Resource Manager?

Sim. A API REST para **osProfile** é semelhante à API REST da VM padrão.

Inclua **osProfile** em seu modelo:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Este bloco JSON é usado no [modelo de início rápido do GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

O perfil do SO também é usado no [modelo de início rápido do GitHub grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Para obter mais informações, veja [Criar ou atualizar um conjunto de dimensionamento de máquinas virtuais](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Como remover certificados preteridos?

Para remover os certificados preteridos, remova o antigo certificado da lista de certificados do cofre. Deixe todos os certificados que você deseja manter em seu computador na lista. Isso não remove o certificado de todas as suas VMs. Também não adiciona o certificado às novas VMs que são criadas no conjunto de dimensionamento de máquinas virtuais.

Para remover o certificado das VMs existentes, você deve escrever uma extensão de script personalizada para remover manualmente os certificados do armazenamento de certificados.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Como injeto uma chave pública SSH existente na camada SSH do conjunto de dimensionamento de máquinas virtuais durante o provisionamento? Quero armazenar os valores da chave pública SSH no Azure Key Vault e utilizá-los no modelo do Resource Manager.

Se você estiver fornecendo as VMs apenas com uma chave SSH pública, não será necessário colocar as chaves públicas no Cofre de Chaves. As chaves públicas não são secretas.

Você pode fornecer as chaves públicas SSH em texto sem formatação ao criar uma VM do Linux:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

Nome do elemento linuxConfiguration | Obrigatório | Type | DESCRIÇÃO
--- | --- | --- | --- 
ssh | Não  | Coleção | Especifica a configuração da chave SSH para um SO Linux
caminho | Sim | Cadeia de caracteres | Especifica o caminho de arquivo do Linux no qual as chaves SSH ou o certificado deve estar localizado
keyData | Sim | Cadeia de caracteres | Especifica uma chave pública SSH codificada em base64

Para obter um exemplo, consulte [o modelo de início rápido do GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando executo `Update-AzVmss` depois de adicionar mais de um certificado no mesmo cofre de chaves, posso ver a seguinte mensagem:

>Update-AzVmss: Segredo da lista contém instâncias repetidas de\<my-subscription-id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, o que não é permitido.

Isso poderá ocorrer se você tentar adicionar novamente o mesmo cofre, em vez de usar um novo certificado de cofre para o cofre de origem existente. O comando `Add-AzVmssSecret` não funcionará corretamente se você estiver adicionando segredos adicionais.

Para adicionar mais segredos do mesmo cofre de chaves, atualize a lista $vmss.properties.osProfile.secrets[0].vaultCertificates.

Para ver a estrutura de entrada esperada, consulte [Criar ou atualizar um conjunto de máquinas virtuais](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Localize o segredo no objeto do conjunto de dimensionamento de máquinas virtuais que está no cofre de chaves. Em seguida, adicione a referência do certificado (a URL e o nome do armazenamento de segredos) à lista associada ao cofre.

> [!NOTE]
> No momento, você não pode remover os certificados das VMs usando a API do conjunto de dimensionamento de máquinas virtuais.
>

As novas VMs não terão o certificado antigo. No entanto, as VMs que têm o certificado e já foram implantadas terão o certificado antigo.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Posso enviar certificados para o conjunto de dimensionamento de máquinas virtuais sem fornecer a senha quando o certificado está no armazenamento de segredos?

Você não precisa codificar as senhas nos scripts. Você pode recuperar dinamicamente as senhas com as permissões usadas para executar o script de implantação. Se você tiver um script que move um certificado a partir do cofre de chaves do armazenamento de segredos, o comando `get certificate` do armazenamento de segredos também gerará a senha do arquivo .pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Como funciona a propriedade Secrets de virtualMachineProfile.osProfile de um conjunto de dimensionamento de máquinas virtuais? Por que preciso do valor sourceVault quando tenho que especificar o URI absoluto para um certificado usando a propriedade certificateUrl?

Uma referência do certificado WinRM (Gerenciamento Remoto do Windows) deve estar presente na propriedade Secrets do perfil do SO.

A finalidade de indicar o cofre de origem é impor as políticas de ACL (lista do controle de acesso) que existem no modelo do Serviço de Nuvem do Azure. Se o cofre de origem não for especificado, os usuários sem permissões para implantar ou acessar os segredos em um cofre de chaves conseguiriam passar por um CRP (Provedor de Recursos de Computação). As ACLs existem até para os recursos que não existem.

Se você fornecer uma ID do cofre de origem incorreta, mas uma URL válida do cofre, um erro será informado quando você sondar a operação.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se eu adicionar segredos a um conjunto de dimensionamento de máquinas virtuais existente, os segredos serão injetados nas VMs existentes ou apenas nas novas?

Os certificados são adicionados a todas as suas VMs, mesmo as preexistentes. Se a propriedade upgradePolicy do conjunto de dimensionamento de máquinas virtuais for definida para **manual**, o certificado será adicionado à VM quando você fizer uma atualização manual na VM.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Onde coloco os certificados para as VMs do Linux?

Para saber como implantar os certificados para VMs do Linux, consulte [Implantar os certificados para as VMs a partir de um cofre de chaves gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como adiciono um novo certificado do cofre a um novo objeto de certificado?

Para adicionar um certificado do cofre a um segredo existente, consulte o exemplo do PowerShell a seguir. Use apenas um objeto de segredo.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece com os certificados se você recria a imagem de uma VM?

Se você recriar a imagem de uma VM, os certificados serão excluídos. Recriar a imagem excluir o disco do SO inteiro.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se você exclui um certificado do cofre de chaves?

Se o segredo for excluído do cofre de chaves e você executar `stop deallocate` para todas as VMs e iniciá-las novamente, encontrará uma falha. A falha ocorre porque o CRP precisa recuperar os segredos no cofre de chaves, mas não consegue. Neste cenário, você pode excluir os certificados do modelo do conjunto de dimensionamento de máquinas virtuais.

O componente CRP não persiste os segredos do cliente. Se você executar `stop deallocate` para todas as VMs no conjunto de dimensionamento de máquinas virtuais, o cache será excluído. Neste cenário, os segredos são recuperados no cofre de chaves.

Você não tem esse problema ao dimensionar porque há uma cópia em cache do segredo no Service Fabric do Azure (no modelo de locatário com um fabric).

### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Por que tenho que especificar o local exato para a URL do certificado (https:\/\/\<nome do cofre >.vault.azure.net:443/secrets/\<exato local >), conforme indicado na [do Service Fabric cenários de segurança de cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?

A documentação do Azure Key Vault informa que a API REST para Obter Segredo deve retornar a versão mais recente do segredo caso a versão não seja especificada.

Método | URL
--- | ---
GET | <https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}>

Substitua o {*nome-segredo*} pelo nome e substitua {*versão-segredo*} pela versão do segredo que você deseja recuperar. A versão do segredo pode ser excluída. Neste caso, a versão atual é recuperada.

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Por que é necessário especificar a versão do certificado quando uso o Cofre de Chaves?

A finalidade da exigência do Cofre de Chaves para especificar a versão do certificado é deixar claro para o usuário qual certificado é implantado em suas VMs.

Se você criar uma VM e atualizar o segredo no cofre de chaves, o novo certificado não será baixado para suas VMs. Mas, suas VMs parecem fazer referência a ele e as novas VMs obtêm o novo segredo. Para evitar isso, é necessário fazer referência a uma versão do segredo.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Minha equipe trabalha com vários certificados que são distribuídos para nós como chaves públicas .cer. Qual é a abordagem recomendada para implantar esses certificados em um conjunto de dimensionamento de máquinas virtuais?

Para implantar as chaves públicas .cer em um conjunto de dimensionamento de máquinas virtuais, você pode gerar um arquivo .pfx que contém somente arquivos .cer. Para fazer isso, use `X509ContentType = Pfx`. Por exemplo, carregue o arquivo .cer como um objeto x509Certificate2 no C# ou no PowerShell, em seguida, chame o método.

Para obter mais informações, consulte [Método X509Certificate.Export (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Não vejo uma opção para os usuários passarem os certificados como cadeias de caracteres base64. A maioria dos outros provedores de recursos tem essa opção.

Para emular a passagem de um certificado como uma cadeia de caracteres base64, é possível extrair a URL com a versão mais recente em um modelo do Resource Manager. Inclua a seguinte propriedade JSON em seu modelo do Resource Manager:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>É necessário encapsular os certificados nos objetos JSON no cofre de chaves?

Nos conjuntos de dimensionamento de máquinas virtuais, os certificados devem ser encapsulados nos objetos JSON.

Também damos suporte ao tipo de conteúdo application/x-pkcs12.

Atualmente não temos suporte para os arquivos .cer. Para usar os arquivos .cer, exporte-os para os contêineres .pfx.



## <a name="compliance-and-security"></a>Conformidade e segurança

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Os conjuntos de dimensionamento de máquinas virtuais são compatíveis com o PCI?

Os conjuntos de dimensionamento de máquinas virtuais são uma camada de API fina sobre o CRP. Ambos os componentes fazem parte da plataforma de computação na árvore de serviços do Azure.

De uma perspectiva de conformidade, os conjuntos de dimensionamento de máquinas virtuais são uma parte fundamental da plataforma de computação do Azure. Eles compartilham uma equipe, ferramentas, processos, metodologia de implantação, controles de segurança, compilação JIT (just-in-time), monitoramento, alertas e outros, com o próprio CRP. Os conjuntos de dimensionamento de máquinas virtuais são compatíveis com o PCI (Payment Card Industry) porque o CRP faz parte do atestado DSS (Data Security Standard) do PCI atual.

Para obter mais informações, consulte o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>[ identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) funcionam com conjuntos de escala de máquina virtual?

Sim. Veja alguns modelos MSI de exemplo nos modelos de Início Rápido do Azure. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).


## <a name="extensions"></a>Extensões

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Como excluo uma extensão do conjunto de dimensionamento de máquinas virtuais?

Para excluir uma extensão do conjunto de dimensionamento de máquinas virtuais, use o seguinte exemplo do PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Você pode encontrar o valor extensionName em `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Há que exemplo de modelo que se integra com os logs do Azure Monitor do conjunto de dimensionamento de máquinas virtuais?

Para exemplo de modelo que se integra com os logs do Azure Monitor do conjunto de dimensionamento de máquinas virtuais, consulte o segundo exemplo na [implantar um cluster do Service Fabric do Azure e habilitar o monitoramento por meio de logs do Azure Monitor](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>A extensões parecem ser executadas em paralelo nos conjuntos de dimensionamento de máquinas virtuais. Isso faz com que minha extensão de script personalizada falhe. O que fazer para corrigir isso?

Para saber mais sobre o sequenciamento de extensão nos conjuntos de dimensionamento de máquinas virtuais, consulte o [Sequenciamento de extensão nos conjuntos de dimensionamento de máquinas virtuais do Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Como redefino a senha para VMs em meu conjunto de dimensionamento de máquinas virtuais?

Há duas maneiras de alterar a senha de VMs em conjuntos de escala.

- Altere o modelo de conjunto de escala de máquina virtual diretamente. Disponível com Computação API 2017-12-01 e posterior.

    Atualize as credenciais de administrador diretamente no modelo do conjunto de escala (por exemplo usando o Gerenciador de recursos do Azure, PowerShell ou CLI). Depois que o conjunto de escala é atualizado, todas as novas máquinas virtuais têm as novas credenciais. Máquinas virtuais existentes têm somente as novas credenciais caso tenham as imagens refeitas.

- Redefina a senha usando as extensões de acesso da VM.

    Use o seguinte exemplo do PowerShell:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Como adiciono uma extensão a todas as VMs em meu conjunto de dimensionamento de máquinas virtuais?

Se a política de atualização estiver definida para **automática**, reimplantar o modelo com as novas propriedades de extensão atualiza todas as VMs.

Se a política de atualização estiver definida para **manual**, primeiro atualize a extensão, em seguida, atualize manualmente todas as instâncias em suas VMs.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Se as extensões associadas a um conjunto de dimensionamento de máquinas virtuais existente forem atualizadas, isso afetará as VMs existentes? (Ou seja, as VMs *não* coincidirão com o modelo do conjunto de dimensionamento de máquinas virtuais?) Ou serão ignoradas? Quando uma máquina existente tem o serviço reparado ou a imagem recriada, os scripts configurados atualmente no conjunto de dimensionamento de máquinas virtuais são executados ou os scripts configurados quando a VM foi criada pela primeira vez são usados?

Se a definição da extensão no modelo do conjunto de dimensionamento de máquinas virtuais for atualizado e a propriedade upgradePolicy for definida para **automática**, as VMs serão atualizadas. Se a propriedade upgradePolicy for definida para **manual**, as extensões serão sinalizadas como não correspondendo ao modelo.

Se uma VM existente tiver o serviço reparado, parecerá uma reinicialização e as extensões não serão executadas novamente. Se a imagem for recriada, será como substituir a unidade do SO pela imagem de origem. Qualquer especialização do modelo mais recente, como as extensões, será executada.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Como faço para ingressar em um conjunto de dimensionamento de máquinas virtuais para um domínio do Active Directory?

Para ingressar em um conjunto de dimensionamento de máquinas virtuais para um domínio do Active Directory (AD), você pode definir uma extensão.

Para definir uma extensão, use a propriedade JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Minha extensão do conjunto de dimensionamento de máquinas virtuais está tentando instalar algo que requer uma reinicialização. Por exemplo, "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

Se sua extensão do conjunto de dimensionamento de máquinas virtuais estiver tentando instalar algo que requer uma reinicialização, você poderá usar a extensão do DSC (Configuração do Estado Desejado) de Automação do Azure. Se o sistema operacional for o Windows Server 2012 R2, o Azure fará a instalação do Windows Management Framework (WMF) 5.0, irá reinicializar e continuar com a configuração.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Como ativo o antimalware em meu conjunto de dimensionamento de máquinas virtuais?

Para ativar o antimalware em seu conjunto de dimensionamento de máquinas virtuais, use o seguinte exemplo do PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Preciso executar um script personalizado hospedado em uma conta de armazenamento privado. O script é executado com êxito quando o armazenamento é público, mas quando tento usar uma SAS (Assinatura de Acesso Compartilhado), ele falha. Esta mensagem é exibida: "Parâmetros obrigatórios ausentes para uma Assinatura de Acesso Compartilhado válida". O link+SAS funcionam bem em meu navegador local.

Para executar um script personalizado hospedado em uma conta de armazenamento privado, defina as configurações protegidas com a chave da conta de armazenamento e o nome. Para obter mais informações, consulte [Extensão de Script Personalizado para o Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="networking"></a>Rede

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>É possível atribuir um NSG (Grupo de Segurança de Rede) a um conjunto de dimensionamento, de forma que ele seja aplicado a todas as NICs da VM no conjunto?

Sim. Um Grupo de Segurança de Rede pode ser aplicado diretamente a um conjunto de dimensionamento referenciando-o na seção networkInterfaceConfigurations do perfil de rede. Exemplo:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Como faço uma troca de VIP para os conjuntos de dimensionamento de máquinas virtuais na mesma assinatura e mesma região?

Se você tiver dois conjuntos de dimensionamento de máquina virtual com front-ends do Azure Load Balancer e eles estiverem na mesma assinatura e região, poderá desalocar os endereços IP públicos de cada um deles e atribuir ao outro. Confira [Troca de VIP: Implantação azul-verde no Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) para ver um exemplo. Isso significa um atraso, já que os recursos são desalocados/alocados no nível da rede. Uma opção mais rápida é usar o Gateway de Aplicativo do Azure com dois pools de back-end e uma regra de roteamento. Como alternativa, você pode hospedar o aplicativo no [serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), que fornece suporte para a alternância rápida entre slots de preparo e de produção.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Como especifico um intervalo de endereços IP privados para usar na alocação estática do endereço IP privado?

Os endereços IPs são selecionados em uma sub-rede que você especifica.

O método de alocação dos endereços IP do conjunto de dimensionamento de máquinas virtuais é sempre "dinâmico", mas isso não significa que esses endereços IP podem mudar. Neste caso, "dinâmico" significa apenas que você não especifica o endereço IP em uma solicitação PUT. Especifique conjunto estático usando a sub-rede.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Como implanto um conjunto de dimensionamento de máquinas virtuais para uma rede virtual do Azure existente?

Para implantar um conjunto de dimensionamento de máquinas virtuais para uma rede virtual do Azure existente, consulte [Implantar um conjunto de dimensionamento de máquinas virtuais para uma rede virtual existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Como adiciono o endereço IP da primeira VM em um conjunto de dimensionamento de máquinas virtuais à saída de um modelo?

Para adicionar o endereço IP da primeira VM em um conjunto de dimensionamento de máquinas virtuais à saída de um modelo, confira [Azure Resource Manager: Obter os IPs particulares dos conjuntos de dimensionamento de máquinas virtuais](https://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Posso usar conjuntos de escala com Rede Acelerada?

Sim. Para usar a rede acelerada, defina enableAcceleratedNetworking como true nas configurações de networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo,
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Como posso configurar os servidores DNS usados por um conjunto de dimensionamento?

Para criar um conjunto de dimensionamento de máquina virtual com uma configuração personalizada do DNS, adicione um pacote JSON dnsSettings à seção de networkInterfaceConfigurations do conjunto de dimensionamento. Exemplo:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Como posso configurar um conjunto de dimensionamento a fim de atribuir um endereço IP público para cada VM?

Para criar um conjunto de dimensionamento de VM que atribui um endereço IP público a cada VM, verifique se a versão da API do recurso Microsoft.Compute/virtualMAchineScaleSets é 2017-03-30 e adicione um pacote JSON _publicipaddressconfiguration_ à seção ipConfigurations do conjunto de dimensionamento. Exemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Posso configurar um conjunto de dimensionamento para funcionar com vários Gateways de Aplicativo?

Sim. Você pode adicionar a ID de recurso de vários pools de endereços de back-end do Gateway de Aplicativo à lista _applicationGatewayBackendAddressPools_ na seção _ipConfigurations_ do perfil de rede do conjunto de dimensionamento.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>No qual situação eu criaria um conjunto de dimensionamento de máquinas virtuais com menos de duas VMs?

Um motivo para criar um conjunto de dimensionamento de máquinas virtuais com menos de duas VMs seria para usar as propriedades elásticas de um conjunto de dimensionamento. Por exemplo, você pode implantar um conjunto de dimensionamento de máquinas virtuais com zero VM para definir a infraestrutura sem pagar os custos de execução da VM. Então, quando você estiver pronto para implantar as VMs, aumente a "capacidade" do conjunto de dimensionamento de máquinas virtuais para a contagem das instâncias de produção.

Outro motivo para criar um conjunto de dimensionamento de máquinas virtuais com menos de duas VMs é se você estiver menos preocupado com disponibilidade do que com o uso de um conjunto de disponibilidade com VMs separadas. Os conjuntos de dimensionamento de máquinas virtuais fornecem uma maneira de trabalhar com unidades de computação não diferenciadas que são intercambiáveis. Essa uniformidade é um diferencial importante para os conjuntos de dimensionamento de máquinas virtuais versus os conjuntos de disponibilidade. Muitas cargas de trabalho sem estado não controlam as unidades individuais. Se a carga de trabalho cair, você poderá reduzir verticalmente para uma unidade de computação e escalar verticalmente para muitas quando a carga de trabalho aumentar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Como altero o número de VMs em um conjunto de dimensionamento de máquinas virtuais?

Para alterar o número de VMs em uma escala de máquina virtual definido no portal do Azure, da configuração de escala VM à seção de propriedades, clique na folha de "Escala" e use a barra deslizante. Para outras maneiras de mudar o contador de instância, veja [Mudar o contador de instância de uma configuração de escala de máquina virtual](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como defino alertas personalizados para quando determinados limites são atingidos?

Há alguma flexibilidade em como lidar com os alertas para os limites especificados. Por exemplo, você pode definir webhooks personalizados. O exemplo de webhook a seguir é de um modelo do Resource Manager:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```

Neste exemplo, um alerta é emitido para Pagerduty.com quando um limite é atingido.

## <a name="patching-and-operations"></a>Aplicação de patch e operações

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Como fazer para criar um conjunto de dimensionamento em um grupo de recursos existente?

A criação de conjuntos de dimensionamento em um grupo de recursos existente ainda não é possível no portal do Azure, mas você pode especificar um grupo de recursos existente ao implantar um conjunto de dimensionamento de um modelo do Azure Resource Manager. Também é possível especificar um grupo de recursos existente ao criar um conjunto de dimensionamento usando o Azure PowerShell ou a CLI.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>É possível mover um conjunto de dimensionamento para outro grupo de recursos?

Sim, é possível mover recursos do conjunto de dimensionamento para uma nova assinatura ou um novo grupo de recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Como atualizo meu conjunto de dimensionamento de máquinas virtuais para uma nova imagem? Como gerencio os patches?

Para atualizar o conjunto de dimensionamento de máquinas virtuais para uma nova imagem e gerenciar os patches, consulte [Atualizar um conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Posso usar a operação de recriação de imagem para redefinir uma VM sem alterar a imagem? (Ou seja, quero redefinir uma VM para as configurações de fábrica, em vez de uma nova imagem.)

Sim, você pode usar a operação de recriação de imagem para redefinir uma VM sem alterar a imagem. No entanto, se o conjunto de dimensionamento de máquinas virtuais fizer referência a uma imagem da plataforma com `version = latest`, sua VM poderá atualizar para uma imagem do SO posterior ao chamar `reimage`.

Para obter mais informações, consulte [Gerenciar todas as VMs em um conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>É possível integrar conjuntos de dimensionamento com os logs do Azure Monitor?

Sim, você pode, instalando a extensão do Azure Monitor na escala de VMs do conjunto. Veja um exemplo da CLI do Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Você pode encontrar o workspaceId e o workspaceKey necessários no espaço de trabalho d oLog Analytics do portal do Azure. Na página Visão Geral, clique no bloco Configurações. Clique na guia Fontes Conectadas na parte superior.

> [!NOTE]
> Se o conjunto de dimensionamento _upgradePolicy_ é definido como Manual, você precisa aplicar a extensão a todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>solução de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Como ativo o diagnóstico de inicialização?

Para ativar o diagnóstico de inicialização, primeiro, crie uma conta de armazenamento. Em seguida, coloque este bloco JSON em seu conjunto de dimensionamento de máquinas virtuais **virtualMachineProfile** e atualize o conjunto de dimensionamento:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando uma nova VM for criada, a propriedade InstanceView da VM mostrará os detalhes da captura de tela etc. Aqui está um exemplo:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Propriedades de máquina virtual

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Como obtenho informações de propriedade para cada VM sem fazer várias chamadas? Por exemplo, como eu obteria o domínio de falha para cada uma das 100 VMs em meu conjunto de dimensionamento de máquinas virtuais?

Para obter informações de propriedade para cada VM sem fazer várias chamadas, você poderá chamar `ListVMInstanceViews` usando um `GET` da API REST no URI de recurso a seguir:

/subscriptions/<id_assinatura>/resourceGroups/<nome_grupo_recursos>/providers/Microsoft.Compute/virtualMachineScaleSets/<nome_conjunto_dimensionamento>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Posso passar argumentos de extensão diferentes para diferentes VMs em um conjunto de dimensionamento de máquinas virtuais?

Não, você não pode passar argumentos de extensão diferentes para diferentes VMs em um conjunto de dimensionamento de máquinas virtuais. No entanto, as extensões podem agir com base nas propriedades exclusivas da VM na qual estão em execução, como no nome da máquina. As extensões também podem consultar os metadados de instância em http://169.254.169.254 para obter mais informações sobre a VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Por que existem lacunas entre os nomes de máquina e as IDs da VM do conjunto de dimensionamento de máquinas virtuais? Por exemplo:  0, 1, 3...

Existem lacunas entre os nomes de máquina e as IDs da VM do conjunto de dimensionamento de máquinas virtuais, porque a propriedade **overprovision** de seu conjunto de dimensionamento de máquinas virtuais está é definida para o valor padrão **true**. Se o excesso de provisionamento estiver definido para **true**, mais VMs que o solicitado serão criadas. Então, as VMs extras são excluídas. Neste caso, você tem maior confiabilidade na implantação, mas às custas das regras de nomenclatura e de NAT (Network Address Translation) contínuos.

Você pode definir essa propriedade para **false**. Para os conjuntos de dimensionamento de máquinas virtuais pequenos, isso não afeta muito a confiabilidade da implantação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual é a diferença entre excluir uma VM em um conjunto de dimensionamento de máquinas virtuais versus desalocar a VM? Quando devo escolher um ou o outro?

A principal diferença entre excluir uma VM em um conjunto de dimensionamento de máquinas virtuais e desalocar a VM é que `deallocate` não exclui os discos rígidos virtuais (VHDs). Existem custos de armazenamento associados à execução de `stop deallocate`. Você pode usar um ou outro por um dos seguintes motivos:

- Você deseja parar de pagar pela computação, mas quer manter o estado do disco das VMs.
- Você deseja iniciar um conjunto de VMs mais rapidamente do que pode escalar horizontalmente um conjunto de dimensionamento de máquinas virtuais.
  - Em relação a este cenário, você pode ter criado seu próprio mecanismo de dimensionamento automático e deseja um dimensionamento de ponta a ponta mais rápido.
- Você tem um conjunto de dimensionamento de máquinas virtuais distribuído sem uniformidade entre os domínios de falha ou os domínios de atualização. Isso pode ser porque você excluiu seletivamente as VMs ou porque as VMs foram excluídas depois do excesso de provisionamento. Executar `stop deallocate` seguido de `start` no conjunto de dimensionamento de máquinas virtuais distribui uniformemente as VMs em domínios de falha ou domínios de atualização.

### <a name="how-do-i-take-a-snapshot-of-a-vmss-instance"></a>Como posso tirar um instantâneo de uma instância do VMSS?
Crie um instantâneo de uma instância de um VMSS.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"
 
$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
``` 
 
Crie um disco gerenciado do instantâneo.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk') 
```
