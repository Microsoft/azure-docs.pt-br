# <a name="persist-files-in-azure-cloud-shell"></a>Persistir arquivos no Azure Cloud Shell
O Cloud Shell utiliza o armazenamento dos Arquivos do Azure para persistir os arquivos entre as sessões.

## <a name="set-up-a-clouddrive-file-share"></a>Configurar um compartilhamento de arquivos clouddrive
No primeiro início, o Cloud Shell solicita a associação de um compartilhamento de arquivos novo ou existente para persistir arquivos entre as sessões.

> [!NOTE]
> O Bash e o PowerShell compartilham o mesmo compartilhamento de arquivos. Somente um compartilhamento de arquivos pode ser associado á montagem automática no Cloud Shell.

### <a name="create-new-storage"></a>Criar novo armazenamento

Ao usar as configurações básicas e seleciona apenas uma assinatura, o Cloud Shell criará três recursos em seu nome na região com suporte mais próxima de você:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Compartilhamento de arquivos:`cs-<user>-<domain>-com-<uniqueGuid>`

![A configuração da assinatura](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

O compartilhamento de arquivos é montado como `clouddrive` no seu diretório `$Home`. Isso é uma ação única e o compartilhamento de arquivos será montado automaticamente nas sessões subsequentes. 

No Bash, o compartilhamento de arquivos também contém uma imagem de 5 GB criada para você e que automaticamente mantém os dados em seu diretório `$Home`. 

### <a name="use-existing-resources"></a>Usar recursos existentes

Usando a opção avançada, você pode associar recursos existentes. Quando aparecer o prompt de instalação de armazenamento, selecione **Mostrar configurações avançadas** para exibir opções adicionais. Os menus suspensos são filtrados para sua região do Cloud Shell, o armazenamento redundante localmente e as contas de armazenamento com redundância geográfica atribuídos.

No Bash, os compartilhamentos de arquivos existentes recebem uma imagem de 5 GB criada para você manter seu diretório `$Home`.

![A configuração do grupo de recursos](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento criadas no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se você deseja impedir que os usuários criem contas de armazenamento no Cloud Shell, crie uma [Política de recursos do Azure para marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) que seja disparada por essa marca específica.

## <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
As contas de armazenamento do Azure associadas devem residir na mesma região que o computador do Cloud Shell em que você estiver montando.

Para localizar sua região atribuída, você pode:
* Exiba a anotação na caixa de diálogo "Configurações avançadas de armazenamento"
* Fazer referência ao nome da conta de armazenamento criada para você (por exemplo: `cloud-shell-storage-westus`)
* Execute `env` e localize a variável `ACC_LOCATION`

Há um computador do Cloud Shell nas regiões a seguir:
|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Índia Central, Sudeste Asiático|

