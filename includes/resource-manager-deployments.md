## <a name="incremental-and-complete-deployments"></a>Implantações incrementais e completas
Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa. A principal diferença entre esses dois modos é como o Resource Manager lida com os recursos existentes no grupo de recursos que não estão no modelo:

* No modo completo, o Resource Manager **exclui** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo. 
* No modo incremental, o Resource Manager **deixa inalterados** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo.

Para ambos os modos, o Resource Manager tenta provisionar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e suas configurações são as mesmas, a operação resulta em nenhuma alteração. Se você alterar as configurações de um recurso, o recurso será provisionado com as novas configurações. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou o tipo de que você precisa.

Por padrão, o Resource Manager usa o modo incremental.

Para ilustrar a diferença entre os modos incrementais e completos, considere o cenário a seguir.

O **Grupo de recursos existente** contém:

* Recurso A
* Recurso B
* Recurso C

O **Modelo** define:

* Recurso A
* Recurso B
* Recurso D

Quando implantado no modo **incremental**, o grupo de recursos contém:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado no modo **completo**, o Recurso C é excluído. O grupo de recursos contém:

* Recurso A
* Recurso B
* Recurso D
