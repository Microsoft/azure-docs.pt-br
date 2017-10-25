---
title: "Notas de versão do Gerenciador de Armazenamento do Microsoft Azure (Visualização) | Microsoft Docs"
description: "Notas de versão do Gerenciador de Armazenamento do Microsoft Azure (Visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: 6010c0ef002730fd4a0020e0e2d61a6176a11cba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Notas de versão do Gerenciador de Armazenamento do Microsoft Azure (Visualização)

Este artigo contém as notas de versão para a versão do Gerenciador de Armazenamento do Azure 0.8.16 (Versão Prévia), além das notas de versão para versões anteriores.

O [Gerenciador de Armazenamento do Microsoft Azure (Visualização)](./vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux.

## <a name="version-0816-preview"></a>Versão 0.8.16 (Versão Prévia)
8/21/2017

### <a name="download-azure-storage-explorer-0816-preview"></a>Baixar o Gerenciador de Armazenamento do Azure 0.8.16 (Versão Prévia)
- [Gerenciador de Armazenamento do Azure 0.8.16 (Versão Prévia) para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 0.8.16 (Versão Prévia) para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Gerenciador de Armazenamento do Azure 0.8.16 (Versão Prévia) para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo
* Quando você abre um blob, o Gerenciador de Armazenamento solicitará que você carregue o arquivo baixado, se uma mudança for detectada
* Experiência de aprimorada de conexão no Azure Stack
* Melhoria no desempenho de upload/download de muitos arquivos pequenos ao mesmo tempo


### <a name="fixes"></a>Correções
* Para alguns tipos de blob, escolher "substituir" durante um conflito de upload às vezes resultava no reinício do upload. 
* Na versão 0.8.15, os uploads às vezes paralisavam em 99%.
* Ao carregar arquivos em um compartilhamento de arquivos, se você optasse por carregar em um diretório que não ainda não existia, o upload falhava.
* O Gerenciador de Armazenamento estava gerando incorretamente os carimbos de data/hora para assinaturas de acesso compartilhadas e consultas de tabela.


Problemas conhecidos
* Usar uma cadeia de conexão de nome e chave não funciona atualmente. Isso será corrigido na próxima versão. Até lá, você pode usar anexar com o nome e chave.
* Se você tentar abrir um arquivo com um nome de arquivo inválido do Windows, o download resultará em um erro de Arquivo não encontrado.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Esta é uma limitação da biblioteca de Nós de Armazenamento do Azure.
* Depois de concluir o carregamento de um blob, a guia que iniciou o carregamento é atualizada. Isso é uma alteração do comportamento anterior, e também levará você de volta à raiz do contêiner no qual você está.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0814-preview"></a>Versão 0.8.14 (Visualização)
06/22/2017

### <a name="download-azure-storage-explorer-0814-preview"></a>Baixar o Gerenciador de Armazenamento do Azure 0.8.14 (Visualização)
* [Baixar o Gerenciador de Armazenamento do Azure 0.8.14 (Visualização) para Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Baixar o Gerenciador de Armazenamento do Azure 0.8.14 (Visualização) para Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Baixar o Gerenciador de Armazenamento do Azure 0.8.14 (Visualização) para Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Novo

* Versão do Electron atualizada para 1.7.2, a fim de aproveitar as várias atualizações de segurança críticas
* Agora você pode acessar rapidamente o guia de solução de problemas online no menu de ajuda
* [Guia][2] de solução de problemas do Gerenciador de Armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

### <a name="known-issues"></a>Problemas conhecidos

* Os botões na caixa de diálogo de confirmação de exclusão de pasta não registram com os cliques do mouse no Linux. A solução alternativa é usar a tecla Enter
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada. 
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```




## <a name="previous-releases"></a>Versões anteriores

* [Versão 0.8.13](#version-0813)
* [Versão 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Versão 0.8.9 / 0.8.8](#version-089--088)
* [Versão 0.8.7](#version-087)
* [Versão 0.8.6](#version-086)
* [Versão 0.8.5](#version-085)
* [Versão 0.8.4](#version-084)
* [Versão 0.8.3](#version-083)
* [Versão 0.8.2](#version-082)
* [Versão 0.8.0](#version-080)
* [Versão 0.7.20160509.0](#version-07201605090)
* [Versão 0.7.20160325.0](#version-07201603250)
* [Versão 0.7.20160129.1](#version-07201601291)
* [Versão 0.7.20160105.0](#version-07201601050)
* [Versão 0.7.20151116.0](#version-07201511160)


### <a name="version-0813"></a>Versão 0.8.13
05/12/2017

#### <a name="new"></a>Novo

* [Guia][2] de solução de problemas do Gerenciador de Armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

#### <a name="fixes"></a>Correções

* Corrigido: o carregamento do arquivo tinha uma chance maior de causar um erro de falta de memória
* Corrigido: agora você pode entrar com PIN/Cartão inteligente
* Corrigido: a opção Abrir no Portal agora funciona com o Azure China, Azure Alemanha, Azure US Government e Azure Stack
* Corrigido: às vezes, durante o carregamento de uma pasta em um contêiner de blob, um erro "Operação ilegal" ocorria
* Corrigido: selecionar tudo foi desabilitado ao gerenciar instantâneos
* Corrigido: os metadados do blob de base podem ser substituídos após a exibição das propriedades de seus instantâneos

#### <a name="known-issues"></a>Problemas conhecidos

* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão
* Durante a ampliação ou a redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada. 
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>Versão 0.8.12 / 0.8.11 / 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* Agora, o Gerenciador de Armazenamento será fechado automaticamente quando você instalar uma atualização pela notificação de atualização
* O acesso rápido in-loco fornece uma experiência aprimorada para trabalhar com seus recursos acessados com frequência
* Agora, no editor de Contêiner de Blob, você pode ver um à qual máquina virtual um blob concedido pertence
* Agora você pode recolher o painel do lado esquerdo
* Agora, a Descoberta é executada ao mesmo tempo que o download
* Usar Estatísticas nos editores de Contêiner de Blob, Compartilhamento de Arquivos e de Tabela para ver o tamanho de seus recursos ou seleção
* Agora, você pode entrar em contas do Azure Stack com base no AAD (Azure Active Directory). 
* Agora você pode carregar arquivos com mais de 32 MB em contas de armazenamento Premium
* Suporte a acessibilidade aprimorado
* Agora você pode adicionar certificados SSL X.509 confiáveis codificados em Base 64 acessando Editar –&gt; Certificados SSL –&gt; Importar Certificados

#### <a name="fixes"></a>Correções

* Corrigido: depois de atualizar as credenciais de uma conta, às vezes o modo de exibição de árvore não atualizava automaticamente
* Corrigido: a geração de um SAS para tabelas e filas do emulador poderia resultar em uma URL inválida
* Corrigido: agora, as contas de armazenamento Premium podem ser expandidas enquanto um proxy está habilitado
* Corrigido: o botão aplicar na página de gerenciamento de contas não funcionava se você tivesse uma ou nenhuma conta selecionada
* Corrigido: o carregamento de blobs que exigem resoluções de conflitos pode falhar – corrigido no 0.8.11 
* Corrigido: o envio de comentários foi interrompido no 0.8.11 – corrigido no 0.8.12 

#### <a name="known-issues"></a>Problemas conhecidos

* Após a atualização para 0.8.10, você precisará atualizar todas as suas credenciais.
* Durante a ampliação ou redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão.
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros.
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada. 
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>Versão 0.8.9 / 0.8.8
02/23/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Novo

* O Gerenciador de Armazenamento 0.8.9 baixará automaticamente a versão mais recente das atualizações.
* Hotfix: usar um URI SAS gerado pelo portal para anexar uma conta de armazenamento resultaria em um erro.
* Agora você pode criar, gerenciar e promover os instantâneos de blob.
* Agora você pode entrar em contas do Azure China, Azure Alemanha e o Azure US Government.
* Agora você pode alterar o nível de zoom. Use as opções no menu Exibir para Ampliar, Reduzir e Redefinir o Zoom.
* Agora há suporte para caracteres Unicode nos metadados do usuário para blobs e arquivos.
* Aprimoramentos de acessibilidade.
* As notas de versão da próxima versão podem ser exibidas na notificação da atualização. Você também pode exibir as notas de versão atuais no menu Ajuda.

#### <a name="fixes"></a>Correções

* Corrigido: agora, o número da versão é exibido corretamente no Painel de Controle do Windows
* Corrigido: a pesquisa não está mais limitada a 50.000 nós
* Corrigido: o carregamento em um compartilhamento de arquivos continuava indefinidamente se o diretório de destino ainda não existisse
* Corrigido: mais estabilidade para downloads e uploads longos

#### <a name="known-issues"></a>Problemas conhecidos

* Durante a ampliação ou redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão.
* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão.
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos.
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Novo

* Você pode escolher como resolver conflitos no início de uma atualização, download ou sessão de cópia na janela Atividades
* Passe o mouse sobre uma guia para ver o caminho completo do recurso de armazenamento
* Quando você clica em uma guia, sincronizar com seu local no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Corrigido: agora, o Gerenciador de Armazenamento é um aplicativo confiável no Mac
* Corrigido: o Ubuntu 14.04 tem suporte novamente
* Corrigido: às vezes, a interface do usuário de adição de conta pisca ao carregar assinaturas
* Corrigido: às vezes, nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Corrigido: o painel de ações, às vezes, exibido ações vazias
* Corrigido: o tamanho da janela da última sessão fechada agora é retido
* Corrigido: você pode abrir várias guias para o mesmo recurso usando o menu de contexto

#### <a name="known-issues"></a>Problemas conhecidos

* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado ou pode causar exceções sem tratamento
* Pela primeira vez usando o Gerenciador de armazenamento em macOS, você poderá ver vários prompts solicitando permissão do usuário acessar o conjunto de chaves. Sugerimos que você selecione Permitir Sempre para que o prompt não seja exibido novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>Novo

* Você pode agora pin usados com mais frequência serviços para o acesso rápido para facilitar a navegação
* Agora você pode abrir vários editores em guias diferentes. Clique para abrir uma guia temporária; clique duas vezes para abrir uma guia permanente. Você também pode clicar na guia temporária para torná-la uma guia permanente
* Fizemos aperfeiçoamentos de desempenho e estabilidade para carregamentos e downloads, especialmente para grandes arquivos em máquinas rápidas
* Pastas "virtuais" vazias agora podem ser criadas em contêineres de blob
* Introduzimos novamente a pesquisa com escopo com nossa nova pesquisa avançada de subcadeia de caracteres, e agora você tem duas opções de pesquisa: 
    * Pesquisa global – basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa com escopo – clique no ícone de lupa ao lado de um nó, adicione um termo de pesquisa ao final do caminho, ou clique com o botão direito e selecione "Pesquisar aqui"
* Adicionamos vários temas: luz (padrão), escuro, preto em alto contraste e branco em alto contraste. Acesse Editar –&gt; Temas para alterar sua preferência de temas
* Você pode modificar as propriedades do Blob e do arquivo
* Agora, damos suporte mensagens em fila codificadas (base64) e não codificadas
* No Linux, um sistema operacional de 64 bits agora é necessário. Para esta versão, só há suporte para Ubuntu 16.04.1 LTS de 64 bits
* Atualizamos nosso logotipo!

#### <a name="fixes"></a>Correções

* Corrigido: Tela problemas congelamento
* Corrigido: Segurança aprimorada
* Corrigido: às vezes, contas anexadas duplicadas apareciam
* Corrigido: um blob com um tipo de conteúdo indefinido pode gerar uma exceção
* Corrigido: a abertura do Painel de Consulta em uma tabela vazia não era possível
* Corrigido: vários bugs na Pesquisa
* Corrigido: aumento do número de recursos carregados de 50 para 100 ao clicar em "Carregar Mais"
* Corrigido: agora, na primeira execução, se uma conta for conectada, selecionamos todas as assinaturas para essa conta por padrão 

#### <a name="known-issues"></a>Problemas conhecidos

* Esta versão do Gerenciador de Armazenamento não é executada no Ubuntu 14.04
* Para abrir várias guias para o mesmo recurso, não clique continuamente no mesmo recurso. Clique em outro recurso e, depois, volte e clique no recurso original para abri-lo novamente em outra guia 
* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado ou pode causar exceções sem tratamento

10/03/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>Novo

* Agora pode usar as chaves SAS geradas pelo Portal para anexar a Contas de Armazenamento e recursos

#### <a name="fixes"></a>Correções

* Corrigido: às vezes, a condição de corrida durante a pesquisa causava a não expansão dos nós
* Corrigido: "Usar HTTP" não funciona ao conectar-se às Contas de Armazenamento com a chave e o nome da conta
* Corrigido: chaves SAS (especialmente as geradas pelo Portal) retornam um erro de "barra à direita"
* Corrigido: problemas de importação de tabela
    * Às vezes, a chave de partição e chave de linha eram invertidas
    * Não é possível ler as Chaves de Partição "nulas"

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado
* O Azure Stack atualmente não dá suporte a Arquivos, então tentar expandir o Arquivos resultará em um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Novo

* Gera links diretos para contas de armazenamento ou contêineres, filas, tabelas ou arquivo para compartilhar e acessar facilmente os recursos – suporte a Windows e Mac OS
* Pesquise seus contêineres de blob, tabelas, filas, compartilhamentos de arquivos ou contas de armazenamento da caixa de pesquisa
* Agora você pode agrupar cláusulas no construtor de consultas de tabela
* Renomear e copiar/colar contêineres de blob, compartilhamentos de arquivos, tabelas, blobs, pastas de blob, arquivos e diretórios de contas e contêineres anexadas a SAS
* Agora, ao renomear e copiar contêineres de blob e compartilhamentos de arquivos, as propriedades e metadados são preservados

#### <a name="fixes"></a>Correções

* Corrigido: não é possível editar entidades de tabela se elas contiverem propriedades boolianas ou binárias

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Novo

* Renomear os contêineres, tabelas, compartilhamentos de arquivos
* Experiência aprimorada do Construtor de consulta
* Capacidade de salvar e carregar consultas
* Compartilha links diretos para contas de armazenamento ou contêineres, filas, tabelas ou arquivo para compartilhar e acessar facilmente os recursos (somente para Windows – suporte para macOS em breve!)
* Capacidade de gerenciar e configurar regras de CORS

#### <a name="fixes"></a>Correções

* Corrigido: as Contas da Microsoft exigem reautenticação a cada 8 a 12 horas

#### <a name="known-issues"></a>Problemas conhecidos

* Às vezes, a interface do usuário pode parecer congelada – maximizar a janela ajuda a resolver esse problema
* A instalação do macOS pode exigir permissões elevadas
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear compartilhamentos de arquivo, contêineres de blob e tabelas não preserva metadados ou outras propriedades no contêiner, como a cota de compartilhamento de arquivo, o nível de acesso público ou políticas de acesso
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação
* A cópia ou a renomeação de recursos não funciona em contas anexadas a SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Novo

* As Contas de Armazenamento são agrupadas por assinaturas; armazenamento de desenvolvimento e recursos anexados via chave ou SAS são mostrados sob o nó (Local e Anexado)
* Saia das contas no painel "Configurações de Conta do Azure"
* Definir configurações de proxy para habilitar e gerenciar a entrada
* Criar e quebrar concessões de blob
* Abrir contêineres de blob, filas, tabelas e arquivos com um único clique

#### <a name="fixes"></a>Correções

* Corrigido: mensagens em fila inseridas com bibliotecas .NET ou Java não são corretamente decodificadas com base64
* Corrigido: tabelas $metrics não são exibidas para contas de Armazenamento de Blobs
* Corrigido: o nó tabelas não funciona para o armazenamento local (Desenvolvimento)

#### <a name="known-issues"></a>Problemas conhecidos

* A instalação do macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Novo

* Suporte para compartilhamento de arquivos: exibição, upload, download, cópia de arquivos e diretórios, URIs SAS (criar e conectar)
* Melhor experiência do usuário para se conectar ao Armazenamento com URIs SAS ou chaves de conta
* Exportar resultados da consulta de tabela
* Personalização e reordenação da coluna Tabela
* Exibição de contêineres de blob $logs e tabelas $metrics para Contas de Armazenamento com métricas habilitadas
* Comportamento de exportação e importação aprimorado, agora inclui o tipo de valor da propriedade

#### <a name="fixes"></a>Correções

* Corrigido: o upload ou download de blobs grandes pode resultar em uploads/downloads incompletos
* Corrigido: edição, adição ou importação de uma entidade com um valor de cadeia de caracteres numérico ("1") converterá esse valor em duplo
* Corrigido: não é possível expandir o nó Tabela no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas conhecidos

* As tabelas $metrics não são visíveis para contas de Armazenamento de Blobs
* Mensagens em fila adicionadas por meio de programação podem não ser exibidas corretamente se as mensagens forem codificadas usando a codificação Base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>Novo

* Melhor tratamento de erros para falhas de aplicativo

#### <a name="fixes"></a>Correções

* Correção de bug no qual as mensagens da Barra de Informações às vezes não apareciam quando as credenciais de entrada eram exigidas

#### <a name="known-issues"></a>Problemas conhecidos

* Tabelas: adição, edição ou importação de uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1.0", e o usuário tenta enviá-lo como um `Edm.String`, o valor voltará por meio da API do cliente como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Novo

* Suporte à tabela: exibição, consulta, exportação, importação e operações CRUD para entidades
* Suporte à fila: exibição, adição, remoção de mensagens da fila
* Gerar URIs SAS para Contas de Armazenamento
* Conectando-se a contas de armazenamento com URIs SAS
* Notificações de atualização para atualizações futuras do Gerenciador de Armazenamento
* Aparência atualizada

#### <a name="fixes"></a>Correções

* Aprimoramento de desempenho e confiabilidade

### <a name="known-issues-amp-mitigations"></a>Problemas &amp; mitigações conhecidos

* O download de arquivos de blob grandes não funciona corretamente – recomendamos usar AzCopy enquanto resolvemos esse problema 
* As credenciais da conta não serão recuperadas nem armazenadas em cache se a pasta base não puder ser encontrada ou não puder receber gravação
* Se adicionarmos, editarmos ou importamos uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1.0", e o usuário tentar enviá-lo como um `Edm.String`, o valor voltará por meio da API do cliente como um Edm.Double
* Ao importar arquivos CSV com registros de várias linhas, os dados podem ser cortados ou embaralhados

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Desempenho geral aprimorado ao carregar, baixar e copiar blobs

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>Novo

* Suporte a Linux (recursos de paridade para OSX)
* Adicionar contêineres de blob com chave SAS (Assinaturas de Acesso Compartilhado)
* Adicionar Contas de Armazenamento para o Azure China
* Adicionar Contas de Armazenamento com pontos de extremidade personalizados
* Abrir e exibir os blobs de texto e imagem de conteúdo
* Exibir e editar propriedades e metadados de blob

#### <a name="fixes"></a>Correções

* Corrigido: o upload ou download de uma grande quantidade de blobs (+ de 500) pode, às vezes, fazer com que o aplicativo apresente uma tela branca 
* Corrigido: ao definir o nível de acesso público do contêiner de blob, o novo valor não é atualizado até que você defina novamente o foco no contêiner. Além disso, a caixa de diálogo sempre assume o padrão "Nenhum acesso público", e não o valor real atual.
* Suporte geral aprimorado de teclado/acessibilidade e interface do usuário
* A disposição do texto do histórico de trilha quebra automaticamente quando o texto é longo com espaços em branco
* A caixa de diálogo SAS dá suporte à validação de entrada
* O armazenamento local continua disponível mesmo se as credenciais do usuário tiverem expirado
* Quando um contêiner de blob aberto for excluído, o gerenciador de blob no lado direito será fechado

#### <a name="known-issues"></a>Problemas conhecidos

* A instalação do Linux precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade: 
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>Novo

* Versões para macOS e Windows
* Entre para exibir suas Contas de Armazenamento – use sua Conta institucional, Conta da Microsoft, 2FA etc.
* Armazenamento de desenvolvimento local (use o emulador de armazenamento, somente para Windows)
* Suporte ao Azure Resource Manager e ao recurso Clássico
* Criar e excluir blobs, filas ou tabelas
* Procurar blobs, filas ou tabelas específicas
* Explorar o conteúdo de contêineres de blob
* Exibir e navegar pelos diretórios
* Carregar, baixar e excluir blobs e pastas
* Exibir e editar propriedades e metadados de blob
* Gerar as chaves SAS
* Gerenciar e criar SAP (Políticas de Acesso armazenado)
* Procure blobs pelo prefixo
* Arraste e solte arquivos para carregar ou baixar

#### <a name="known-issues"></a>Problemas conhecidos

* Ao definir o nível de acesso público do contêiner de blob, o novo valor não é atualizado até que você defina novamente o foco no contêiner
* Quando você abre a caixa de diálogo para definir o nível de acesso público, ela sempre mostra "Nenhum acesso público" como o padrão, e não o valor real atual
* Não é possível renomear blobs baixados
* Às vezes, as entradas de log de atividade ficam "presas" em um estado em andamento quando ocorre um erro, e o erro não é exibido
* Às vezes falha ou fica completamente branca ao tentar carregar ou baixar uma grande quantidade de blobs
* Às vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um contêiner (blob/fila/tabela), se você inserir um nome inválido e continuar com a criação de outro com um tipo de contêiner diferente, não será possível definir o foco no novo tipo
* Não é possível criar uma nova pasta ou renomear a pasta




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md