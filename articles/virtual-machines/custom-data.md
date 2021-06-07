---
title: Dados personalizados e Máquinas Virtuais do Azure
description: Detalhes sobre como usar Dados personalizados e Cloud-Init em Máquinas Virtuais do Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284907"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dados personalizados e Cloud-Init em Máquinas Virtuais do Azure

Talvez seja necessário injetar um script ou outros metadados em uma máquina virtual Microsoft Azure no momento do provisionamento.  Em outras nuvens, esse conceito é geralmente conhecido como dados do usuário.  No Microsoft Azure, temos um recurso semelhante chamado dados personalizados. 

Os dados personalizados só são disponibilizados para a VM durante a primeira inicialização/configuração inicial, chamamos isso de "provisionamento". O provisionamento é o processo no qual os parâmetros de Criação de VM (por exemplo, nome do host, nome de usuário, senha, certificados, dados personalizados, chaves etc.) são disponibilizados para a VM e um agente de provisionamento os processa, como o [Agente do Linux](./extensions/agent-linux.md) e o [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passando dados personalizados para a VM
Para usar dados personalizados, você deve codificar o conteúdo em base64 antes de passá-lo para a API, a menos que esteja usando uma ferramenta de CLI que faça a conversão para você, como AZ CLI. O tamanho não pode exceder 64 KB.

Na CLI, você pode passar os dados personalizados como um arquivo e ele será convertido em base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

No ARM (Azure Resource Manager), há uma [função base64](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Processando dados personalizados
Os agentes de provisionamento instalados nas VMs manipulam com interface com a plataforma e colocando-os no sistema de arquivos. 

### <a name="windows"></a>Windows
Os dados personalizados são colocados em *%SYSTEMDRIVE%\AzureData\CustomData.bin* como um arquivo binário, mas não são processados. Se você quiser processar esse arquivo, será necessário criar uma imagem personalizada e escrever o código para processar o CustomData.bin.

### <a name="linux"></a>Linux  
Nos sistemas operacionais Linux, os dados personalizados são passados para a VM por meio do arquivo ovf-env.xml, que é copiado para o diretório */var/lib/waagent* durante o provisionamento.  As versões mais recentes do agente do Linux no Microsoft Azure também vão copiar os dados codificados em base64 para */var/lib/waagent/CustomData* para sua conveniência.

Atualmente, o Azure é compatível com dois agentes de provisionamento:
* Agente do Linux – por padrão, o agente não processará dados personalizados; você precisará criar uma imagem personalizada com ela habilitada. As configurações relevantes, de acordo com a [documentação](https://github.com/Azure/WALinuxAgent#configuration) são:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando você habilitar dados personalizados e executar um script, ele atrasará o relatório de que a VM está pronta ou que o provisionamento teve êxito até que o script seja concluído. Se o script exceder o tempo de provisionamento da VM total permitido de 40 minutos, a criação da VM falhará. Observe que, se o script não for executado ou ocorrer erros durante a execução, isso não será considerado uma falha de provisionamento fatal; você precisará criar um caminho de notificação para alertá-lo quanto ao estado de conclusão do script.

Para solucionar problemas de execução de dados personalizados, examine */var/log/waagent.log*

* Cloud-init – processará dados personalizados por padrão e o cloud-init aceita [vários formatos](https://cloudinit.readthedocs.io/en/latest/topics/format.html) de dados personalizados, como configuração de cloud-init, scripts etc. Semelhante ao agente do Linux, quando o cloud-init processa os dados personalizados. Se houver erros durante a execução do processamento de configuração ou de scripts, isso não será considerado uma falha de provisionamento fatal e você precisará criar um caminho de notificação para alertá-lo quanto ao estado de conclusão do script. No entanto, de maneira diferente do agente do Linux, o cloud-init não aguarda a conclusão das configurações de dados personalizados do usuário antes de relatar para a plataforma que a VM está pronta. Para obter mais informações sobre o cloud-init no Azure, examine a [documentação](./linux/using-cloud-init.md).


Para solucionar problemas de execução de dados personalizados, examine a [documentação](./linux/using-cloud-init.md#troubleshooting-cloud-init) de solução de problemas.


## <a name="faq"></a>Perguntas frequentes
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Posso atualizar dados personalizados depois que a VM tiver sido criada?
Para VMs únicas, os dados personalizados no modelo de VM não podem ser atualizados, mas você pode atualizar dados personalizados de VMSS por meio da [API REST](/rest/api/compute/virtualmachinescalesets/update) (não aplicável para clientes PS ou AZ CLI). Quando você atualiza dados personalizados no modelo de VMSS:
* As instâncias existentes nos VMSS não receberão os dados personalizados atualizados antes de refazer a imagem.
* As instâncias existentes nos VMSS que são atualizadas não receberão os dados personalizados atualizados.
* Novas instâncias receberão os novos dados personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Posso colocar valores confidenciais em dados personalizados?
Aconselhamos **não** armazenar dados confidenciais em dados personalizados. Para obter mais informações, confira [Melhores práticas de segurança e criptografia do Azure](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>Os dados personalizados são disponibilizados no IMDS?
Não, esse recurso não está disponível atualmente.
