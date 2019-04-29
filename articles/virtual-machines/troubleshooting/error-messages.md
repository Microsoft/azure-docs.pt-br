---
title: Códigos de erro comuns de VM no Azure | Microsoft Docs
description: Entenda alguns dos códigos de erro comuns encontrados ao provisionar e gerenciar máquinas virtuais no Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 5945be210812a6cbc24c9a3bb12414be5212be17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711196"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Entender as mensagens de erro comuns ao gerenciar máquinas virtuais no Azure

Este artigo descreve alguns dos códigos de erro mais comuns e as mensagens que você pode encontrar ao criar ou gerenciar VMs (máquinas virtuais) no Azure.

>[!NOTE]
> Deixe comentários nesta página sobre mensagens de erro ou nos [comentários do Azure](https://feedback.azure.com/forums/216843-virtual-machines) com o rótulo #azerrormessage.

## <a name="error-response-format"></a>Formato da resposta de erro 
As VMs do Azure usam o seguinte formato JSON para a resposta de erro:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Uma resposta de erro sempre inclui um código de status e um objeto de erro. Cada objeto de erro sempre contém um código de erro e uma mensagem. Se a VM é criada com um modelo, o objeto de erro também contém uma seção de detalhes que contém um nível interno de códigos de erro e a mensagem. Normalmente, o nível mais interno da mensagem de erro é a falha raiz.


## <a name="common-virtual-machine-management-errors"></a>Erros comuns de gerenciamento da máquina virtual

Esta seção lista as mensagens de erro comuns que você pode encontrar ao gerenciar VMs:

|  Código do Erro  |  Mensagem de erro  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Falha ao adquirir concessão durante a criação do disco ‘{0}’ utilizando o blob com o URI {1}. O blob já está em utilização.  |  
|  AllocationFailed  |  Falha na alocação. Experimente reduzir o tamanho da VM ou o número de VMs, volte a tentar mais tarde ou experimente implementar num Conjunto de Disponibilidade diferente ou localização do Azure diferente.  |  
|  AllocationFailed  |  Falha na alocação da VM devido a um erro interno. Tente novamente mais tarde ou tente implantar em um local diferente.  |
|  ArtifactNotFound  |  Não foi possível encontrar a extensão de VM com publicador '{0}' e tipo '{1}' no local '{2}'.  |
|  ArtifactNotFound  |  Não foi possível encontrar a extensão com editor '{0}', tipo '{1}' e versão do manipulador de tipo '{2}' no repositório de extensões.  |
|  ArtifactVersionNotFound  |  Não foi encontrada nenhuma versão no repositório de artefatos que coincida com a versão solicitada ‘{0}’.  |
|  ArtifactVersionNotFound  |  Nenhuma versão encontrada no repositório de artefato que atende a versão solicitada '{0}' para a extensão da VM com o publicador '{1}' e tipo '{2}'.  |
|  AttachDiskWhileBeingDetached  |  Não é possível anexar o disco de dados "{0}" à VM "{1}" porque o disco está sendo desanexado. Aguarde até que o disco esteja completamente desanexado e tente novamente.  |
|  BadRequest  |  Os Conjuntos de disponibilidade alinhados ainda não têm suporte nesta região.  |
|  BadRequest  |  Não há suporte para a adição de uma VM com discos gerenciados a um Conjunto de disponibilidade não gerenciado, ou à adição de uma VM com discos baseados em blob ao Conjunto de disponibilidade gerenciado. Crie um Conjunto de disponibilidade com a propriedade "managed" definida para adicionar uma VM com discos gerenciados a ele.  |
|  BadRequest  |  Não há suporte para Managed Disks nesta região.  |
|  BadRequest  |  Não há suporte para vários VMExtensions por manipulador para o tipo de SO "{0}". A VMExtension "{1}" com o manipulador "{2}" já foi adicionada ou especificada na entrada.  |
|  BadRequest  |  A operação '{0}' não tem suporte no Recurso '{1}' com discos gerenciados.  |
|  CertificateImproperlyFormatted  |  A representação JSON do segredo obtida a partir de {0} tem um campo de dados que não é um ficheiro PFX corretamente formatado ou a palavra-passe fornecida não descodifica corretamente o ficheiro PFX.  |
|  CertificateImproperlyFormatted  |  Os dados obtidos a partir de {0} não são desserializáveis no JSON.  |
|  Conflito  |  O redimensionamento do disco é permitido apenas quando se cria uma VM ou quando a VM é desalocada.  |
|  ConflictingUserInput  |  Não foi possível anexar o disco "{0}", pois ele já pertence à VM "{1}".  |
|  ConflictingUserInput  |  Os grupos de recursos de origem e de destino são os mesmos.  |
|  ConflictingUserInput  |  As contas de armazenamento de origem e de destino do disco {0} são diferentes.  |
|  ContainerAlreadyOnLease  |  Já existe uma concessão no contentor de armazenamento que contém o blob com o URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  A solicitação de movimentação de recursos contém recursos do KeyVault que são referenciados por um ou mais {0}s na solicitação. Não há suporte para isso no momento na Movimentação entre assinaturas. Verifique os detalhes do erro para saber as IDs de recurso do KeyVault Ids.  |
|  DiagnosticsOperationInternalError  |  Ocorreu um erro interno ao processar o perfil de diagnóstico da VM {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0} já está sendo usado por outro disco pertencente à VM "{1}". Examine os metadados do blob para as informações de referência do disco.  |
|  DiskBlobNotFound  |  Não é possível encontrar o blob do VHD com o URI {0} para o disco "{1}".  |
|  DiskBlobNotFound  |  Não é possível encontrar o blob de VHD com o URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  O segredo {0} não tem as marcas de {1}. Atualize a versão secreta, adicione as marcas necessárias e tente novamente.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Cancelar o capsulamento do valor {0} do segredo usando a chave {1} falhou.  |
|  DiskImageNotReady  |  Imagem de disco {0} está no estado {1}. Tente novamente quando a imagem estiver pronta.  |
|  DiskPreparationError  |  Um ou mais erros ocorreram ao preparar discos de VM. Consulte a exibição de instância de disco para obter detalhes.  |
|  DiskProcessingError  |  O processamento do disco foi interrompido, pois a VM tem outros discos em discos com falha.  |
|  ImageBlobNotFound  |  Não é possível encontrar o blob do VHD com o URI {0} para o disco "{1}".  |
|  ImageBlobNotFound  |  Não é possível encontrar o blob de VHD com o URI {0}.  |
|  IncorrectDiskBlobType  |  Blobs de disco só podem ser do tipo blob de páginas. Blob {0} para o disco "{1}" é do tipo blob de blocos.  |
|  IncorrectDiskBlobType  |  Blobs de disco só podem ser do tipo blob de páginas. Blob {0} é do tipo "{1}".  |
|  IncorrectImageBlobType  |  Blobs de disco só podem ser do tipo blob de páginas. Blob {0} para o disco "{1}" é do tipo blob de blocos.  |
|  IncorrectImageBlobType  |  Blobs de disco só podem ser do tipo blob de páginas. Blob {0} é do tipo "{1}".  |
|  InternalOperationError  |  Não foi possível resolver a conta de armazenamento {0}. Certifique-se de que foi criada através do Provedor de Recursos de Armazenamento na mesma localização que o recurso de computação.  |
|  InternalOperationError  |  {0} falha nas tarefas para atingir a meta.  |
|  InternalOperationError  |  Ocorreu um erro na validação do perfil de rede da VM "{0}".  |
|  InvalidAccountType  |  O AccountType {0} é inválido.  |
|  InvalidParameter  |  O valor do parâmetro {0} é inválido.  |
|  InvalidParameter  |  A senha de Admin especificada não é permitida.  |
|  InvalidParameter  |  "A senha fornecida deve ter entre {0}-{1} caracteres e deve atender pelo menos {2} requisitos de complexidade de senha entre os seguintes: <ol><li> Contém um caractere maiúsculo</li><li>Contém um caractere minúsculo</li><li>Contém um dígito numérico</li><li>Contém um caractere especial.</li></ol>  |
|  InvalidParameter  |  O Nome de Utilizador de Admin especificado não é permitido.  |
|  InvalidParameter  |  Não é possível anexar um disco de SO existente quando a VM é criada a partir de uma imagem de plataforma ou de utilizador.  |
|  InvalidParameter  |  O nome de contêiner {0} é inválido. Os nomes de contêiner devem ter de três a 63 caracteres e conter somente caracteres alfanuméricos minúsculos e hífen. Hífen deve ser precedido e seguido por um caractere alfanumérico.  |
|  InvalidParameter  |  O nome de contêiner {0} na URL {1} é inválido. Os nomes de contêiner devem ter de três a 63 caracteres e conter somente caracteres alfanuméricos minúsculos e hífen. Hífen deve ser precedido e seguido por um caractere alfanumérico.  |
|  InvalidParameter  |  O nome de blob na URL {0} contém uma barra invertida. Atualmente, não há suporte para isso em discos.  |
|  InvalidParameter  |  O URI {0} não parece ser um URI de blob correto.  |
|  InvalidParameter  |  Um disco chamado "{0}" já utiliza o mesmo LUN: {1}.  |
|  InvalidParameter  |  Já existe um disco chamado "{0}".  |
|  InvalidParameter  |  Não é possível especificar substituições da imagem de utilizador para um disco que já está definido na referência de imagem especificada.  |
|  InvalidParameter  |  Um disco chamado "{0}" já utiliza a mesma URL do VHD {1}.  |
|  InvalidParameter  |  A contagem especificada de domínios de falha de {0} deve estar no intervalo de {1} a {2}.  |
|  InvalidParameter  |  O tipo de licença {0} é inválido. Os tipos de licença válidos são: Windows_Client ou Windows_Server, diferencia maiúsculas de minúsculas.  |
|  InvalidParameter  |  O nome do host Linux não pode ter mais de {0} caracteres de comprimento ou conter os seguintes caracteres: {1}.  |
|  InvalidParameter  |  O caminho de destino das chaves públicas Ssh está limitado ao valor predefinido {0} devido a um problema conhecido no agente de provisionamento do Linux.  |
|  InvalidParameter  |  Já existe um disco no LUN {0}.  |
|  InvalidParameter  |  A assinatura {0} da solicitação deve corresponder à assinatura {1} contida na ID de disco gerenciado.  |
|  InvalidParameter  |  Os dados personalizados de OSProfile têm de ter uma codificação Base64 e um comprimento máximo de {0} caracteres.  |
|  InvalidParameter  |  O nome do blob na URL {0} deve terminar com a extensão "{1}".  |
|  InvalidParameter  |  {0}‘ não é um prefixo de nome de blob VHD capturado válido. Um prefixo válido deve corresponder à expressão "{1}".  |
|  InvalidParameter  |  Não é possível adicionar certificados à VM caso o agente de VM não esteja aprovisionado.  |
|  InvalidParameter  |  Já existe um disco no LUN {0}.  |
|  InvalidParameter  |  Não é possível criar a VM, pois o tamanho solicitado {0} não está disponível no cluster em que o conjunto de disponibilidade está alocado no momento. Os tamanhos disponíveis são: {1}. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  O tamanho de VM {0} solicitado não está disponível nesta região. Os tamanhos disponíveis nesta região são: {1}. Saiba mais sobre os tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  O tamanho de VM {0} solicitado não está disponível nesta região. Saiba mais sobre os tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  O nome de utilizador de admin do Windows não pode ter mais de {0} caracteres, terminar com um ponto final (.) ou conter os seguintes caracteres: {1}.  |
|  InvalidParameter  |  O nome de computador do Windows não pode ter mais de {0} caracteres, só conter números ou conter os seguintes caracteres: {1}.  |
|  MissingMoveDependentResources  |  A solicitação de movimentação de recursos não contém todos os recursos dependentes. Verifique nos detalhes do erro se há IDs de recursos que estão faltando.  |
|  MoveResourcesHaveInvalidState  |  A solicitação de Movimentação de recursos contém VMs que estão associados a contas de armazenamento inválidas. Verifique os detalhes dessas IDs de recurso e os nomes de conta de armazenamento referenciados.  |
|  MoveResourcesHavePendingOperations  |  A solicitação de movimentação de recursos contém recursos para os quais há uma operação pendente. Verifique os detalhes dessas IDs de recurso. Repita a operação depois de concluir as operações pendentes.  |
|  MoveResourcesNotFound  |  A solicitação de movimentação de recursos contém recursos que não podem ser encontrados. Verifique os detalhes dessas IDs de recurso.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido  |
|  NetworkingInternalOperationError  |  Ocorreu um erro interno no processamento do perfil de rede da VM.  |
|  NotFound  |  Não é possível encontrar o Conjunto de Disponibilidade {0}.  |
|  NotFound  |  A Máquina Virtual de origem "{0}" especificada na solicitação não existe neste local do Azure.  |
|  NotFound  |  Locatário com ID {0} não encontrado.  |
|  NotFound  |  A Imagem {0} não foi encontrada.  |
|  NotSupported  |  O tipo da licença é {0}, mas o blob de imagem {1} não é local.  |
|  OperationNotAllowed  |  Não é possível excluir o Conjunto de disponibilidade {0}. Antes de eliminar um Conjunto de disponibilidade, verifique se ele não contém nenhuma VM.  |
|  OperationNotAllowed  |  Não é permitido alterar o SKU do conjunto de disponibilidade de "Alinhado" para "Clássico".  |
|  OperationNotAllowed  |  Não é possível modificar as extensões numa VM que não está a ser executada.  |
|  OperationNotAllowed  |  A ação de Captura tem suporte apenas em uma Máquina Virtual com discos baseados em blob. Use as APIs de recurso de "Imagem" para criar uma imagem de uma Máquina Virtual gerenciada.  |
|  OperationNotAllowed  |  O recurso {0} não pode ser criado por meio da Imagem {1} até que a Imagem seja criada com êxito.  |
|  OperationNotAllowed  |  As atualizações de encryptionSettings não são permitidas quando a VM está alocada, repita a operação depois de a VM ser desalocada  |
|  OperationNotAllowed  |  A adição de um disco gerido a uma VM com disco baseado em blob não é suportada.  |
|  OperationNotAllowed  |  O número máximo de discos de dados permitido para anexação a uma VM deste tamanho é de {0}.  |
|  OperationNotAllowed  |  A adição de um disco baseado em blob a uma VM com discos geridos não é suportada.  |
|  OperationNotAllowed  |  A operação "{0}" não é permitida na Imagem "{1}" porque a mesma está marcada para eliminação. Você só pode repetir a operação de Exclusão (ou aguardar a conclusão de uma em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque a VM é generalizada.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida, pois a coleção de ponto de restauração '{1}' está marcada pra exclusão.  |
|  OperationNotAllowed  |  A operação "{0}" não é permitida na extensão de VM "{1}" dado que está marcada para eliminação. Você só pode repetir a operação de Exclusão (ou aguardar a conclusão de uma em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida porque as Máquinas Virtuais '{1}' estão sendo provisionadas usando a Imagem '{2}'.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida, uma vez que o ScaleSet '{1}' da Máquina Virtual está usando a Imagem '{2}' atualmente.  |
|  OperationNotAllowed  |  A operação "{0}" não é permitida na VM "{1}" dado que a VM está marcada para eliminação. Você só pode repetir a operação de Exclusão (ou aguardar a conclusão de uma em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', já que a VM está desalocada ou marcada para ser desalocada.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque a VM está em execução. Desligue explicitamente caso encerre a VM a partir do sistema operacional convidado.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', já que a VM não foi desalocada.  |
|  OperationNotAllowed  |  Operação '{0}' não é permitida na VM '{1}', pois a VM tem a extensão '{2}' em estado de falha.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque outra operação está em andamento.  |
|  OperationNotAllowed  |  A operação '{0}' requer que a Máquina Virtual '{1}' seja Generalizada.  |
|  OperationNotAllowed  |  A operação requer que a VM esteja em execução (ou definida para ser executada).  |
|  OperationNotAllowed  |  Não é permitido o disco com tamanho de {0} GB, que é menor que o tamanho de {1} GB do disco correspondente na Imagem.  |
|  OperationNotAllowed  |  As Extensões de Conjunto de Dimensionamento de VM do processador "{0}" podem ser adicionadas apenas aquando da criação do Conjunto de Dimensionamento de VM.  |
|  OperationNotAllowed  |  As Extensões de Conjunto de Dimensionamento de VM do processador "{0}" podem apenas ser eliminadas aquando da eliminação do Conjunto de Dimensionamento de VM.  |
|  OperationNotAllowed  |  A VM "{0}" já está usando discos gerenciados.  |
|  OperationNotAllowed  |  A VM "{0}" pertence ao conjunto de disponibilidade "Clássico" "{1}". Atualize a conjunto de disponibilidade para usar o SKU "Alinhado" e repita a Conversão.  |
|  OperationNotAllowed  |  VM criada a partir da imagem não pode ter discos baseados em blob. Todos os discos precisam ser discos gerenciados.  |
|  OperationNotAllowed  |  A operação de captura não pode ser concluída porque a VM não está generalizada.  |
|  OperationNotAllowed  |  Não há permissão para operações de gerenciamento de VM "{0}", pois os discos de VM estão sendo convertidos em discos gerenciados.  |
|  OperationNotAllowed  |  Uma operação em andamento está alterando o estado de energia da Máquina Virtual {0} para {1}. Execute a operação {2} após algum tempo.  |
|  OperationNotAllowed  |  Não é possível adicionar ou atualizar a VM. Talvez o tamanho de VM {0} solicitado não esteja disponível na unidade de alocação existente. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não é possível redimensionar a VM, pois o tamanho solicitado {0} não está disponível no cluster em que o conjunto de disponibilidade está alocado no momento. Os tamanhos disponíveis são: {1}. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não é possível redimensionar a VM, pois o tamanho solicitado {0} não está disponível no cluster em que a VM está alocada no momento. Para redimensionar a VM para {1}, desaloque (esta é a operação de Parada no Portal do Azure) e tente novamente a operação de redimensionamento. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Falha no provisionamento do SO para a VM "{0}" porque o SO convidado está sendo provisionado.  |
|  OSProvisioningClientError  |  Falha no provisionamento do SO para a VM "{0}". Detalhes do erro: {1} Verifique se a imagem foi preparada corretamente (generalizada). <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Falha na geração da chave host de SSH. Detalhes do erro: {0}. Para resolver esse problema, verifique se o agente do Linux está configurado corretamente. <ul><li>Você pode verificar as instruções em: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  O nome de usuário especificado para a VM é inválido para essa distribuição do Linux. Detalhes do erro: {0}.  |
|  OSProvisioningInternalError  |  Falha no provisionamento do SO para a VM "{0}" devido a um erro interno.  |
|  OSProvisioningTimedOut  |  O provisionamento do SO para a VM "{0}" não foi concluído no tempo alocado. A VM ainda pode concluir o provisionamento com êxito. Verifique mais tarde o estado do provisionamento.  |
|  OSProvisioningTimedOut  |  O provisionamento do SO para a VM "{0}" não foi concluído no tempo alocado. A VM ainda pode concluir o provisionamento com êxito. Verifique mais tarde o estado do provisionamento. Verifique também se a imagem foi preparada corretamente (generalizada).   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  O provisionamento do SO para a VM "{0}" não foi concluído no tempo alocado. No entanto, o agente convidado da VM foi detectado como em execução. Isso sugere que o SO convidado não foi corretamente preparado para ser usado como uma imagem de VM (com CreateOption=FromImage). Para resolver esse problema, use o VHD como é feito em CreateOption=Attach, ou prepare-o corretamente para uso como uma imagem:   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  O tamanho de VM necessário não está atualmente disponível na localização selecionada.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  De momento, não é possível atualizar o recurso devido a uma atualização de plataforma em curso. Tente novamente mais tarde.  |
|  StorageAccountLimitation  |  A conta de armazenamento "{0}" não suporta os blobs de página necessários à criação dos discos.  |
|  StorageAccountLimitation  |  A conta de armazenamento “{0}” excedeu sua cota alocada.  |
|  StorageAccountLocationMismatch  |  Não foi possível resolver a conta de armazenamento {0}. Certifique-se de que foi criada através do Provedor de Recursos de Armazenamento na mesma localização que o recurso de computação.  |
|  StorageAccountNotFound  |  Conta de armazenamento {0} não encontrada. Certifique-se de que a conta de armazenamento não foi eliminada e que pertence à mesma localização do Azure da VM.  |
|  StorageAccountNotRecognized  |  Utilize uma conta de armazenamento gerida pelo Provedor de Recursos de Armazenamento. Não há suporte para o uso de {0}.  |
|  StorageAccountOperationInternalError  |  Ocorreu um erro interno durante o acesso à conta de armazenamento {0}.  |
|  StorageAccountSubscriptionMismatch  |  A conta de armazenamento {0} não pertence à subscrição {1}.  |
|  StorageAccountTooBusy  |  Conta de armazenamento "{0}" está muito ocupada no momento. Considere usar outra conta.  |
|  StorageAccountTypeNotSupported  |  O disco {0} usa {1} que é uma conta de Armazenamento de Blobs. Tente novamente com uma conta de armazenamento de finalidade geral.  |
|  StorageAccountTypeNotSupported  |  A conta de armazenamento {0} é do tipo {1}. O Diagnóstico de inicialização oferece suporte a {2} tipos de conta de armazenamento.  <ul><li>Esse erro ocorre se você usar a conta de armazenamento premium para o diagnóstico de inicialização. Para saber mais, consulte [Como usar o diagnóstico de inicialização](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  A assinatura não está autorizada.  |
|  TargetDiskBlobAlreadyExists  |  O blob {0} já existe. Forneça um URI de blob diferente para criar um disco de dados vazio novo "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Não é possível continuar com a operação de captura dado que o blob de imagem de destino {0} já existe e o sinalizador de substituição de blobs de VHD não está definido. Exclua o blob ou defina o sinalizador para substituir blobs VHD e tente novamente.  |
|  TargetDiskBlobAlreadyExists  |  Não é possível continuar com a operação dado que o blob de imagem de destino {0} tem uma concessão ativa.   |
|  TargetDiskBlobAlreadyExists  |  O blob {0} já existe. Forneça um URI de blob diferente como destino para o disco "{1}".  |
|  TooManyVMRedeploymentRequests  |  Foram recebidos demasiados pedidos de reimplementação para a VM "{0}" ou para as VM no mesmo conjunto de disponibilidades que esta VM. Tente novamente mais tarde.  |
|  VHDSizeInvalid  |  O valor de tamanho do disco especificado de {0} para o disco "{1}" com o blob {2} é inválido. O tamanho do disco deve ser de {3} a {4}.  |
|  VMAgentStatusCommunicationError  |  A VM "{0}" não reportou o status do agente de VM ou extensões. Verifique se a VM tem um agente VM em execução e se consegue estabelecer conexões de saída com o armazenamento do Azure.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro durante a comunicação com o repositório de artefatos para obtenção dos detalhes de artefato da VM.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro interno ao obter os dados de artefato da VM a partir do repositório de artefatos.  |
|  VMExtensionHandlerNonTransientError  |  O manipulador '{0}' relatou uma falha na extensão da VM '{1}' com código de erro terminal '{2}' e mensagem de erro: '{3}'  |
|  VMExtensionManagementInternalError  |  Ocorreu um erro interno durante o processamento da extensão de VM "{0}".  |
|  VMExtensionManagementInternalError  |  Vários erros ocorreram ao preparar as extensões da VM. Consulte a exibição de instância de extensão de VM para obter detalhes.  |
|  VMExtensionProvisioningError  |  A VM reportou uma falha durante o processamento da extensão "{0}". Mensagem de erro: “{1}”.  |
|  VMExtensionProvisioningError  |  Houve falha no provisionamento de várias extensões de VM na VM. Consulte a exibição de instância de extensão de VM para obter detalhes.  |
|  VMExtensionProvisioningTimeout  |  Provisionamento de extensão da VM "{0}" expirou. A instalação da extensão pode estar demorando muito tempo, ou não foi possível obtê-la.  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual a partir de uma imagem do tipo não Mercado não necessita de Informações do plano. Remova as Informações do plano existentes no pedido. O nome de disco do SO é {0}.  |
|  VMMarketplaceInvalidInput  |  As informações de compra não coincidem. Não é possível implantar a partir da imagem do Marketplace. O nome de disco do SO é {0}.  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual a partir da imagem de Mercado necessita que o pedido inclua as Informações do plano. O nome de disco do SO é {0}.  |
|  VMNotFound  |  A VM ‘{0}’ não pode ser encontrada.  |
|  VMRedeploymentFailed  |  A reimplementação da VM "{0}" falhou devido a um erro interno. Tente novamente mais tarde.  |
|  VMRedeploymentTimedOut  |  A reimplantação da VM "{0}" não foi concluída no tempo alocado. Ela pode ser concluída a qualquer momento. Caso contrário, repita a solicitação.  |
|  VMStartTimedOut  |  A VM "{0}" não foi iniciada no tempo alocado. A VM ainda pode ser iniciada com êxito. Verifique o estado da energia mais tarde.  |


## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, você pode contatar os especialistas do Azure nos [fóruns do MSDN do Azure e nos fóruns do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.