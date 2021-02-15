---
title: Como importar e exportar Azure AD Connect definições de configuração
description: Este artigo descreve as perguntas frequentes sobre o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681948"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Importar e exportar Azure AD Connect definições de configuração 

As implantações do Azure Active Directory (Azure AD) Connect variam de uma instalação de modo expresso de floresta única para implantações complexas que são sincronizadas em várias florestas usando regras de sincronização personalizadas. Devido ao grande número de opções e mecanismos de configuração, é essencial entender quais configurações estão em vigor e ser capaz de implantar rapidamente um servidor com uma configuração idêntica. Esse recurso apresenta a capacidade de catalogar a configuração de um determinado servidor de sincronização e importar as configurações para uma nova implantação. Instantâneos de configurações de sincronização diferentes podem ser comparados para visualizar facilmente as diferenças entre dois servidores ou o mesmo servidor ao longo do tempo.

Cada vez que a configuração é alterada no assistente de Azure AD Connect, um novo arquivo de configurações JSON com carimbo de data/hora é exportado automaticamente para **%ProgramData%\AADConnect**. O nome do arquivo de configurações está no formato **aplicado-SynchronizationPolicy-*. JSON**, em que a última parte do nome do arquivo é um carimbo de data/hora.

> [!IMPORTANT]
> Somente as alterações feitas por Azure AD Connect são exportadas automaticamente. Todas as alterações feitas usando o PowerShell, o Synchronization Service Manager ou o editor de regras de sincronização devem ser exportadas sob demanda conforme necessário para manter uma cópia atualizada. A exportação sob demanda também pode ser usada para fazer uma cópia das configurações em um local seguro para fins de recuperação de desastre.

## <a name="export-azure-ad-connect-settings"></a>Exportar configurações de Azure AD Connect 

Para exibir um resumo de suas definições de configuração, abra a ferramenta Azure AD Connect e selecione a tarefa adicional denominada **Exibir ou exportar a configuração atual**. Um resumo rápido de suas configurações é mostrado junto com a capacidade de exportar a configuração completa do servidor.

Por padrão, as configurações são exportadas para **%ProgramData%\AADConnect**. Você também pode optar por salvar as configurações em um local protegido para garantir a disponibilidade se ocorrer um desastre. As configurações são exportadas usando o formato de arquivo JSON e não devem ser criadas ou editadas manualmente para garantir a consistência lógica. A importação de um arquivo criado manualmente ou editado não tem suporte e pode levar a resultados inesperados.

## <a name="import-azure-ad-connect-settings"></a>Importar configurações de Azure AD Connect

Para importar configurações exportadas anteriormente:
 
1. Instale o **Azure ad Connect** em um novo servidor.
1. Selecione a opção **Personalizar** após a página de **boas-vindas** .
1. Selecione **Importar configurações de sincronização**. Procure o arquivo de configurações JSON exportado anteriormente.
1. Selecione **Instalar**.

   ![Captura de tela que mostra as telas instalar componentes necessários](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Substitua as configurações nesta página, como o uso de SQL Server em vez de LocalDB ou o uso de uma conta de serviço existente em vez de um VSA padrão. Essas configurações não são importadas do arquivo de definições de configuração. Eles estão lá para fins de comparação e informações.

### <a name="import-installation-experience"></a>Importar experiência de instalação 

A experiência de instalação de importação é mantida intencionalmente simples com entradas mínimas do usuário para fornecer facilmente reprodução de um servidor existente.

Aqui estão as únicas alterações que podem ser feitas durante a experiência de instalação. Todas as outras alterações podem ser feitas após a instalação do assistente de Azure AD Connect:
- **Credenciais de Azure Active Directory**: o nome da conta para o administrador global do Azure usado para configurar o servidor original é sugerido por padrão. Ele *deve*   ser alterado se você quiser sincronizar informações em um novo diretório.
- **Entrada do usuário**: as opções de logon configuradas para o servidor original são selecionadas por padrão e solicitam automaticamente as credenciais ou outras informações necessárias durante a configuração. Em casos raros, pode haver a necessidade de configurar um servidor com opções diferentes para evitar a alteração do comportamento do servidor ativo. Caso contrário, selecione **Avançar** para usar as mesmas configurações.
- **Credenciais de diretório local**: para cada diretório local incluído nas configurações de sincronização, você deve fornecer credenciais para criar uma conta de sincronização ou fornecer uma conta de sincronização personalizada pré-criada. Esse procedimento é idêntico à experiência de instalação limpa com a exceção de que você não pode adicionar ou remover diretórios.
- **Opções de configuração**: como com uma instalação limpa, você pode optar por definir as configurações iniciais para se iniciar a sincronização automática ou habilitar o modo de preparo. A principal diferença é que o modo de preparo é intencionalmente habilitado por padrão para permitir a comparação dos resultados de configuração e sincronização antes de exportar ativamente os resultados para o Azure.

![Captura de tela que mostra o conectar seus diretórios](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Somente um servidor de sincronização pode estar na função primária e exportar ativamente as alterações de configuração para o Azure. Todos os outros servidores devem ser colocados no modo de preparo.

## <a name="migrate-settings-from-an-existing-server"></a>Migrar configurações de um servidor existente 

Se um servidor existente não oferecer suporte ao gerenciamento de configurações, você poderá optar por atualizar o servidor in-loco ou migrar as configurações para uso em um novo servidor de preparo.

A migração requer a execução de um script do PowerShell que extrai as configurações existentes para uso em uma nova instalação.Use esse método para catalogar as configurações do servidor existente e aplicá-las a um servidor de preparo recém-instalado.A comparação das configurações do servidor original com um servidor recém-criado visualizará rapidamente as alterações entre os servidores.Como sempre, siga o processo de certificação da sua organização para garantir que nenhuma configuração adicional seja necessária.

### <a name="migration-process"></a>Processo de migração 
Para migrar as configurações:

1. Inicie **AzureADConnect.msi** no novo servidor de preparo e pare na página de **boas-vindas** do Azure ad Connect.

1. Copie **MigrateSettings.ps1** do diretório Microsoft Azure ad Connect\Tools para um local no servidor existente. Um exemplo é C:\setup, em que a instalação é um diretório que foi criado no servidor existente.

   ![Captura de tela que mostra os diretórios Azure AD Connect.](media/how-to-connect-import-export-config/migrate1.png)

1. Execute o script conforme mostrado aqui e salve todo o diretório de configuração de servidor de nível inferior. Copie esse diretório para o novo servidor de preparo. Você deve copiar toda a pasta **exported-ServerConfiguration-** _ para o novo servidor.

   ![Captura de tela que mostra o script no Windows PowerShell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Captura de tela que mostra a cópia da pasta exportada-ServerConfiguration-_.](media/how-to-connect-import-export-config/migrate3.png)

1. Inicie **Azure ad Connect** clicando duas vezes no ícone na área de trabalho. Aceite os termos de licença para software Microsoft e, na próxima página, selecione **Personalizar**.
1. Marque a caixa de seleção **Importar configurações de sincronização** . Selecione **procurar** para procurar a pasta copiada-over-ServerConfiguration-*. Selecione o MigratedPolicy.jsem para importar as configurações migradas.

   ![Captura de tela que mostra a opção Importar configurações de sincronização.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Verificação pós-instalação 

Comparar o arquivo de configurações originalmente importado com o arquivo de configurações exportado do servidor implantado recentemente é uma etapa essencial na compreensão de quaisquer diferenças entre a implantação pretendida em comparação com a do resultado. Usar seu aplicativo de comparação de texto lado a lado favorito produz uma visualização instantânea que realça rapidamente as alterações desejadas ou acidentais.

Embora muitas das etapas de configuração manual já sejam eliminadas, você ainda deve seguir o processo de certificação da sua organização para garantir que nenhuma configuração adicional seja necessária. Essa configuração pode ocorrer se você usar configurações avançadas, que atualmente não são capturadas nesta versão do gerenciamento de configurações.

Aqui estão as limitações conhecidas:
- **Regras de sincronização**: a precedência para uma regra personalizada deve estar no intervalo reservado de 0 a 99 para evitar conflitos com as regras padrão da Microsoft. Colocar uma regra personalizada fora do intervalo reservado pode fazer com que a regra personalizada seja deslocada conforme as regras padrão são adicionadas à configuração. Um problema semelhante ocorrerá se sua configuração contiver regras padrão modificadas. A modificação de uma regra padrão não é recomendada e o posicionamento da regra provavelmente estará incorreto.
- **Write-back do dispositivo**: essas configurações são catalogadas. Atualmente, eles não são aplicados durante a configuração. Se o Write-back do dispositivo tiver sido habilitado para o servidor original, você deverá configurar manualmente o recurso no servidor implantado recentemente.
- **Tipos de objeto sincronizados**: embora seja possível restringir a lista de tipos de objetos sincronizados (como usuários, contatos e grupos) usando o Synchronization Service Manager, atualmente, não há suporte para esse recurso por meio das configurações de sincronização. Depois de concluir a instalação, você deve reaplicar manualmente a configuração avançada.
- **Perfis de execução personalizados**: embora seja possível modificar o conjunto padrão de perfis de execução usando o Synchronization Service Manager, atualmente, não há suporte para esse recurso por meio das configurações de sincronização. Depois de concluir a instalação, você deve reaplicar manualmente a configuração avançada.
- **Configurando a hierarquia de provisionamento**: esse recurso avançado do Synchronization Service Manager não tem suporte por meio de configurações de sincronização. Ele deve ser reconfigurado manualmente após a conclusão da implantação inicial.
- **Serviços de Federação do Active Directory (AD FS) (AD FS) e autenticação PingFederate**: os métodos de logon associados a esses recursos de autenticação são selecionados automaticamente. Você deve fornecer todos os outros parâmetros de configuração necessários interativamente.
- **Uma regra de sincronização personalizada desabilitada será importada como habilitada**: uma regra de sincronização personalizada desabilitada será importada como habilitada. Certifique-se de desabilitá-lo no novo servidor também.

 ## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Instalar agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
