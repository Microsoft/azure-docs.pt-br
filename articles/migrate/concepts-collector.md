---
title: Dispositivo Coletor nas Migrações para Azure | Microsoft Docs
description: Fornece informações sobre o dispositivo Coletor nas Migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 224511b9748c540f2cd48a3d8393a9c74f76ce32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679218"
---
# <a name="about-the-collector-appliance"></a>Sobre o dispositivo Coletor

 Este artigo fornece informações sobre o Coletor de Migrações para Azure.

O Coletor de Migrações para Azure é um dispositivo leve que pode ser usado para descobrir um ambiente do vCenter local para fins de avaliação com o serviço [Migrações para Azure](migrate-overview.md), antes da migração para o Azure.  

## <a name="discovery-method"></a>Método de descoberta

Anteriormente, havia duas opções para o dispositivo coletor, descoberta única e contínua. O modelo de descoberta única foi preterido pois se baseou em configurações de estatísticas do vCenter Server para coleta de dados de desempenho (configurações de estatísticas necessárias a ser definido para o nível 3) e também coletados contadores médios (em vez de pico) que resultou em dimensionamento insuficiente. O modelo de detecção contínua garante a coleta de dados granulares e resulta em dimensionamento preciso devido à coleção de contadores de pico. Veja como isso funciona:

O appliance Collector é continuamente conectado ao projeto de Migrações para Azure e coleta continuamente os dados de desempenho das VMs.

- O Coletor cria perfis continuamente do ambiente local para coletar dados de utilização em tempo real a cada 20 segundos.
- O dispositivo acumula as amostras de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o dispositivo seleciona o valor de pico das amostras de 20 segundos e envia-o para o Azure.
- Esse modelo não depende das configurações de estatísticas do vCenter Server para coletar dados de desempenho.
- Você pode parar a criação de perfil contínua a qualquer momento do Coletor.

**Avaliações rápidas:** Com o dispositivo de descoberta contínua, após a conclusão da descoberta (leva algumas horas dependendo do número de VMs), você poderá criar avaliações imediatamente. Já que a coleta de dados de desempenho começa quando você inicia a descoberta, se você estiver procurando avaliações rápidas, deverá escolher o critério de dimensionamento na avaliação como *local*. Para avaliações baseadas no desempenho, é aconselhável esperar pelo menos um dia após o início da descoberta para obter recomendações de tamanhos confiáveis.

O dispositivo coleta apenas dados de desempenho continuamente e não detecta nenhuma alteração de configuração no ambiente local (ou seja, adição de VM, exclusão, adição de disco, etc.). Se houver uma alteração de configuração no ambiente local, você poderá fazer o seguinte para refletir as alterações no portal:

- Adição de itens (VMs, discos, núcleos, etc.): Para refletir essas alterações no portal do Azure, você pode interromper a descoberta do dispositivo e iniciá-la novamente. Isso garantirá que as alterações sejam atualizadas no projeto de Migrações para Azure.

- Exclusão de VMs: Devido à maneira como o dispositivo é projetado, a exclusão de VMs não é refletida, mesmo se você parar e iniciar a descoberta. Isso ocorre porque os dados das descobertas subsequentes são anexados a descobertas antigas e não substituídos. Nesse caso, você pode simplesmente ignorar a VM no portal, removendo-a do grupo e recalculando a avaliação.

> [!NOTE]
> O dispositivo de descoberta única já foi preterido, pois esse método se baseou nas configurações de estatísticas do vCenter Server para disponibilidade de pontos de dados de desempenho e contadores de desempenho médios coletados, o que resultou em subdimensionamento de VMs para migração para o Azure.

## <a name="deploying-the-collector"></a>Implantando o Coletor

Você pode implantar o dispositivo Coletor usando um modelo OVF:

- Baixe o modelo OVF de um projeto das Migrações para Azure no portal do Azure. Importe o arquivo baixado para o vCenter Server para configurar a VM do dispositivo Coletor.
- No OVF, o VMware define uma VM com 8 núcleos, 16 GB de RAM e um disco de 80 GB. O sistema operacional é o Windows Server 2016 R2 (64 bits).
- Quando você executa o Coletor, são executadas várias verificações de pré-requisitos para garantir que o Coletor possa se conectar às Migrações para Azure.

- [Saiba mais](tutorial-assessment-vmware.md#create-the-collector-vm) sobre como criar o Coletor.


## <a name="collector-prerequisites"></a>Pré-requisitos do coletor

O Coletor precisa passar por algumas verificações de pré-requisitos para conferir se ele pode se conectar ao serviço de Migrações para Azure na Internet e fazer upload dos dados descobertos.

- **Verifique a nuvem do Azure**: O coletor precisa saber a nuvem do Azure para o qual você planeja migrar.
    - Selecione Azure Governamental se você estiver planejando migrar para a nuvem do Azure Governamental.
    - Selecione Azure Global se você estiver planejando migrar para a nuvem do Azure Governamental.
    - Com base em nuvem especificada aqui, o dispositivo enviará metadados descobertos para os respectivos pontos de extremidade.
- **Verificar conexão com a Internet**: O coletor pode conectar-se à Internet diretamente ou por meio de um proxy.
    - A verificação de pré-requisitos confere a conectividade com as [URLs obrigatórias e opcionais](#urls-for-connectivity).
    - Se houver uma conexão direta com a Internet, não será necessária nenhuma ação específica, além de conferir se o coletor pode acessar as URLs necessárias.
    - Se você estiver se conectando por meio de um proxy, observe os requisitos abaixo.
- **Verificar sincronização de horário**: O Coletor deve ser sincronizado com o servidor de horário de internet para garantir que as solicitações para o serviço sejam autenticadas.
    - A URL portal.azure.com deverá estar acessível no Coletor para que o horário possa ser validado.
    - Se o computador não estiver sincronizado, será necessário alterar o horário do relógio na VM do Coletor para corresponder ao horário atual. Para fazer isso, abra um prompt de administrador na VM e execute **w32tm /tz** para verificar o fuso horário. Execute **w32tm /resync** para sincronizar o horário.
- **O serviço do Coletor deve estar em execução**:  O serviço Coletor de Migrações para Azure deve estar em execução na VM do Coletor.
    - Esse serviço é iniciado automaticamente quando o computador é inicializado.
    - Se o serviço não estiver em execução, inicie-o usando o Painel de Controle.
    - O serviço do coletor conecta-se ao vCenter Server, coleta os dados de desempenho e os metadados da VM e envia para o serviço de Migrações para Azure.
- **Verifique se o VMware PowerCLI 6.5 está instalado**: O módulo PowerShell VMware PowerCLI 6.5 precisa estar instalado na VM do Coletor, para que ele possa se comunicar com o vCenter Server.
    - Se o Coletor puder acessar as URLs necessárias para instalar o módulo, ele será instalado automaticamente durante a implantação do Coletor.
    - Se o Coletor não puder instalar o módulo durante a implantação, você precisará instalá-lo manualmente.
- **Verifique a conexão ao VCenter Server**: O Coletor precisa ser capaz de conectar-se ao vCenter Server e consultar as VMs, seus metadados e os contadores de desempenho. [Verificar pré-requisitos](#connect-to-vcenter-server) para conectar-se.


### <a name="connect-to-the-internet-via-a-proxy"></a>Conectar-se à Internet por meio de um proxy

- Se o servidor proxy exigir autenticação, você poderá especificar o nome de usuário e a senha ao configurar o Coletor.
- O endereço IP/FQDN do servidor Proxy deve ser especificado como *http:\//IPaddress* ou *http:\//FQDN*.
- Há suporte apenas para o proxy HTTP. Os servidores proxy baseados em HTTPS não têm suporte do Coletor.
- Se o servidor proxy for um proxy de interceptação, importe o certificado de proxy para a VM do Coletor.
  1. Na VM do coletor, acesse **Menu Iniciar** > **Gerenciar certificados do computador**.
  2. Na ferramenta Certificados, em **Certificados – Computador Local**, localize **Editores Confiáveis** > **Certificados**.

      ![Ferramenta de Certificados](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Copie o certificado de proxy para a VM do Coletor. Você precisará obtê-lo do seu administrador de rede.
  4. Clique duas vezes para abrir o certificado e, em seguida, clique em **Instalar Certificado**.
  5. No Assistente para Importação de Certificados > Localização do Repositório, escolha **Computador Local**.

     ![Local do repositório de certificados](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. Selecione **Colocar todos os certificados no repositório a seguir** > **Procurar** > **Editores Confiáveis**. Clique em **Concluir** para importar o certificado.

     ![Repositório de certificados](./media/concepts-intercepting-proxy/certificate-store.png)

  7. Verifique se o certificado foi importado conforme o esperado e se a verificação de pré-requisitos de conectividade com a Internet funciona conforme o esperado.


### <a name="urls-for-connectivity"></a>URLs para conectividade

A verificação de conectividade é validada conectando-se a uma lista de URLs.

**URL** | **Detalhes**  | **Verificação de pré-requisitos**
--- | --- | ---
*. portal.azure.com | Aplicável para o Azure Global. Verifica a conectividade com o serviço do Azure e sincronização de horário. | Acesso à URL necessário.<br/><br/> A verificação de pré-requisitos falhará se não houver nenhuma conectividade.
*.portal.azure.us | Aplicável somente ao Microsoft Azure Governamental. Verifica a conectividade com o serviço do Azure e sincronização de horário. | Acesso à URL necessário.<br/><br/> A verificação de pré-requisitos falhará se não houver nenhuma conectividade.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Usado para baixar o módulo do PowerShell vCenter PowerCLI. | Acesso a URLs é necessário.<br/><br/> A verificação de pré-requisitos não falhará.<br/><br/> A instalação automática do módulo na VM do Coletor falhará. Você precisará instalar o módulo manualmente em uma máquina que tenha conectividade com a internet e, em seguida, copie os módulos para o dispositivo. [Saiba mais, vá para a etapa 4 deste guia de solução de problemas](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Instalar manualmente o módulo VMware PowerCLI

1. Instale o módulo usando [estas etapas](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Estas etapas descrevem a instalação online e offline.
2. Se a VM do Coletor estiver offline e instalar o módulo em um computador diferente com acesso à Internet, você precisará copiar os arquivos VMware.* desse computador para a VM do Coletor.
3. Após a instalação, você poderá reiniciar as verificações de pré-requisitos para confirmar se o PowerCLI está instalado.

### <a name="connect-to-vcenter-server"></a>Conectar-se ao vCenter Server

O Coletor conecta-se ao vCenter Server e consulta os metadados e os contadores de desempenho da VM. Aqui está o que você precisa para a conexão.

- Há suporte apenas para o vCenter Server versões 5.5, 6.0, 6.5 e 6.7.
- Você precisa de uma conta somente leitura com as permissões resumidas abaixo para descoberta. Somente data centers acessíveis com a conta podem ser acessados para descoberta.
- Por padrão você se conecta ao vCenter Server com um endereço IP ou FQDN. Se o vCenter Server escutar em uma porta diferente, conecte-se a ele usando o formato *IPAddress:Port_Number* ou *FQDN:Port_Number*.
- O Coletor deve ter uma linha de visão de rede para o vCenter Server.

#### <a name="account-permissions"></a>Permissões da conta

**Conta** | **Permissões**
--- | ---
Pelo menos uma conta de usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura   


## <a name="collector-communications"></a>Comunicações do Coletor

O Coletor comunica-se de acordo com o resumo no diagrama e na tabela a seguir.

![Diagrama de comunicação do coletor](./media/tutorial-assessment-vmware/portdiagram.PNG)


**O Coletor comunica-se com** | **Porta** | **Detalhes**
--- | --- | ---
Serviço Migrações para Azure | TCP 443 | O Coletor comunica-se com o serviço de Migrações para Azure por SSL 443.
vCenter Server | TCP 443 | O Coletor precisa ser capaz de comunicar-se com o vCenter Server.<br/><br/> Por padrão, ele se conecta ao vCenter na 443.<br/><br/> Se o vCenter Server escuta em uma porta diferente, essa porta precisa estar disponível como a porta de saída no Coletor.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Metadados coletados

> [!NOTE]
> Os metadados descobertos pelas migrações para Azure dispositivo coletor é usado para ajudá-lo dimensione seus aplicativos ao migrar para o Azure, executar análise de adequação do Azure, análise de dependência de aplicativo e planejamento de custo. Microsoft não usa esses dados em relação a qualquer auditoria de conformidade de licença.

O dispositivo Coletor descobre os seguintes de metadados de configuração para cada VM. Os dados de configuração da VM ficam disponíveis uma hora após o início da descoberta.

- Nome de exibição da VM (no vCenter Server)
- Caminho de inventário da VM (o host ou a pasta no vCenter Server)
- Endereço IP
- Endereço MAC
- Sistema operacional
- Número de núcleos, discos, NICs
- Tamanho da memória, tamanhos de disco
- Contadores de desempenho da VM, do disco e da rede.

### <a name="performance-counters"></a>contadores de desempenho

 O dispositivo coletor coleta os seguintes contadores de desempenho para cada VM do host ESXi em um intervalo de 20 segundos. Esses contadores são contadores do vCenter e, embora a terminologia diga média, as amostras de 20 segundos são contadores de tempo real. Os dados de desempenho para as VMs começam a ficar disponíveis no portal duas horas depois de você ter iniciado a descoberta. É altamente recomendável aguardar pelo menos um dia antes de criar avaliações com base no desempenho para obter recomendações precisas de dimensionamento correto. Se você estiver procurando instantâneos, poderá criar avaliações com critérios de dimensionamento como *local*, que não considerará os dados de desempenho para o dimensionamento correto.

**Contador** |  **Impacto na avaliação**
--- | ---
cpu.usage.average | Tamanho de VM recomendado e custo  
mem.usage.average | Tamanho de VM recomendado e custo  
virtualDisk.read.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.write.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberReadAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberWriteAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
net.received.average | Calcula o tamanho da VM                          
net.transmitted.average | Calcula o tamanho da VM     

A lista completa de contadores de VMware coletados pelas Migrações para Azure está disponível abaixo:

**Categoria** |  **Metadados** | **Ponto de dados do vCenter**
--- | --- | ---
Detalhes do computador | ID da VM | vm.Config.InstanceUuid
Detalhes do computador | Nome da VM | vm.Config.Name
Detalhes do computador | ID do vCenter Server | VMwareClient.InstanceUuid
Detalhes do computador |  Descrição da VM |  vm.Summary.Config.Annotation
Detalhes do computador | Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Detalhes do computador | Tipo de sistema operacional | vm.Summary.Config.GuestFullName
Detalhes do computador | Versão do sistema operacional | vm.Summary.Config.GuestFullName
Detalhes do computador | Tempo de inicialização | vm.Config.Firmware
Detalhes do computador | Número de núcleos | vm.Config.Hardware.NumCPU
Detalhes do computador | Megabytes de memória | vm.Config.Hardware.MemoryMB
Detalhes do computador | Número de discos | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
Detalhes do computador | Lista de tamanhos de disco | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
Detalhes do computador | Lista de adaptadores de rede | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
Detalhes do computador | Utilização da CPU | cpu.usage.average
Detalhes do computador | Utilização da memória | mem.usage.average
Detalhes do disco (por disco) | Valor de chave do disco | disk.Key
Detalhes do disco (por disco) | Número de unidades de disco | disk.UnitNumber
Detalhes do disco (por disco) | Valor de chave de controlador de disco | disk.ControllerKey.Value
Detalhes do disco (por disco) | Gigabytes provisionados | virtualDisk.DeviceInfo.Summary
Detalhes do disco (por disco) | Nome do disco | Esse valor é gerado usando disk.UnitNumber, disk.Key e disk.ControllerKey.Value
Detalhes do disco (por disco) | Número de operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Detalhes do disco (por disco) | Número de operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Detalhes do disco (por disco) | Taxa de transferência de leitura em megabytes por segundo | virtualDisk.read.average
Detalhes do disco (por disco) | Taxa de transferência de gravação em megabytes por segundo | virtualDisk.write.average
Detalhes do adaptador de rede (por NIC) | Nome do adaptador de rede | nic.Key
Detalhes do adaptador de rede (por NIC) | Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Detalhes do adaptador de rede (por NIC) | Endereços IPv4 | vm.Guest.Net
Detalhes do adaptador de rede (por NIC) | Endereços IPv6 | vm.Guest.Net
Detalhes do adaptador de rede (por NIC) | Taxa de transferência de leitura em megabytes por segundo | net.received.average
Detalhes do adaptador de rede (por NIC) | Taxa de transferência de gravação em megabytes por segundo | net.transmitted.average
Detalhes do caminho de inventário | NOME | container.GetType().Name
Detalhes do caminho de inventário | Tipo de objeto filho | container.ChildType
Detalhes do caminho de inventário | Detalhes de referência | container.MoRef
Detalhes do caminho de inventário | Caminho de inventário completo | container.Name com caminho completo
Detalhes do caminho de inventário | Detalhes do pai | Container.Parent
Detalhes do caminho de inventário | Detalhes da pasta para cada VM | ((Folder)container).ChildEntity.Type
Detalhes do caminho de inventário | Detalhes do datacenter para cada pasta de VM | ((Datacenter)container).VmFolder
Detalhes do caminho de inventário | Detalhes do datacenter para cada pasta de host | ((Datacenter)container).HostFolder
Detalhes do caminho de inventário | Detalhes do cluster para cada host | ((ClusterComputeResource)container).Host)
Detalhes do caminho de inventário | Detalhes do host para cada VM | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Protegendo o dispositivo Coletor

Recomendamos as seguintes etapas para proteger o dispositivo Coletor:

- Não compartilhe nem deixe as senhas de administrador com partes não autorizadas.
- Desligue o dispositivo quando não estiver em uso.
- Coloque o dispositivo em uma rede segura.
- Após a conclusão da migração, exclua a instância do dispositivo.
- Além disso, após a migração, exclua também os arquivos de backup de disco (VMDKs), pois os discos podem ter as credenciais do vCenter armazenadas em cache.

## <a name="os-license-in-the-collector-vm"></a>Licença de sistema operacional na VM do Coletor

O Coletor é fornecido com uma licença de avaliação do Windows Server 2012 R2 que é válida por 180 dias. Se o período de avaliação da VM do Coletor estiver expirando, baixe um novo OVA e crie outro dispositivo.

## <a name="updating-the-os-of-the-collector-vm"></a>Atualizando o sistema operacional da VM do Coletor

Embora o dispositivo Coletor tenha uma licença de avaliação de 180 dias, você precisará atualizar continuamente o sistema operacional no dispositivo para evitar o desligamento automático do dispositivo.

- Se o Coletor não for atualizado por 60 dias, ele iniciará o desligamento automático da computador.
- Se houver uma descoberta em execução, o computador não será desligado, mesmo após 60 dias. O computador será desligado depois que a descoberta for concluída.
- Se você já usou o Coletor por mais de 60 dias, é recomendado manter o computador sempre atualizado executando o Windows Update.

## <a name="upgrading-the-collector-appliance-version"></a>Atualizando a versão do dispositivo Coletor

É possível atualizar o Coletor para a última versão sem baixar o OVA novamente.

1. Baixe o [pacote de atualização mais recente listado](concepts-collector-upgrade.md)
2. Para garantir que o hotfix baixado é seguro, abra a janela de comando do Administrador e execute o comando a seguir para gerar o hash do arquivo ZIP. O hash gerado deve corresponder ao hash mencionado em relação à versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exemplo de uso C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copie o arquivo zip para a máquina virtual do Coletor de Migrações para Azure (dispositivo do coletor).
4. Clique com o botão direito do mouse no arquivo zip e selecione Extrair Tudo.
5. Clique com o botão direito do mouse em Setup.ps1, selecione Executar com o PowerShell e siga as instruções na tela para instalar a atualização.

## <a name="discovery-process"></a>Processo de descoberta

Depois que o dispositivo estiver configurado, você poderá executar a descoberta. Isso funciona assim:

- Você executa uma descoberta pelo escopo. Todas as VMs no caminho de inventário do vCenter especificado serão descobertas.
    - Você pode definir um escopo de cada vez.
    - O escopo pode incluir 1500 VMs ou menos.
    - O escopo pode ser um datacenter, uma pasta ou um host ESXi.
- Depois de conectar-se ao vCenter Server, conecte-se especificando um projeto de migração para a coleção.
- As VMs são descobertas e seus metadados e dados de desempenho são enviados para o Azure. Essas ações fazem parte de um trabalho de coleta.
    - O dispositivo Coletor recebe uma ID do Coletor específica que persiste em um determinado computador entre as descobertas.
    - Um trabalho de coleta em execução recebe uma ID de sessão específica. A ID é alterada para cada trabalho de coleta e pode ser usada para a solução de problemas.

## <a name="next-steps"></a>Próximas etapas

[Configurar uma avaliação para VMs do VMware locais](tutorial-assessment-vmware.md)
