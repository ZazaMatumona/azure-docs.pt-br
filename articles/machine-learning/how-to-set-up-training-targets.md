---
title: Usar destinos de computação para treinamento de modelo
titleSuffix: Azure Machine Learning
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo de machine learning. É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente. Se precisar escalar horizontalmente, passe para um destino de computação em nuvem.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e83faee7d72026dafc50b21d0a0773e663e5a03a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933097"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurar e usar destinos de computação para treinamento de modelo 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente denominados [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para a implantação de modelo conforme descrito em ["em que local e como implantar seus modelos"](how-to-deploy-and-where.md).

É possível criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, o estúdio do Azure Machine Learning, a CLI do Azure ou a extensão VS Code do Azure Machine Learning. Se você tiver destinos de computação criados por meio de outro serviço (por exemplo, um cluster HDInsight), poderá usá-los anexando-os ao workspace do Azure Machine Learning.
 
Neste artigo, você aprende a usar vários destinos de computação para treinamento do modelo.  As etapas para todos os destinos de computação seguem o mesmo fluxo de trabalho:
1. Escolha __Criar__ um destino de computação se você ainda não tiver um.
2. Escolha __Anexar__ o destino de computação a seu workspace.
3. Escolha __Configurar__ o destino de computação para que ele contenha as dependências de pacote e ambiente Python exigidas pelo seu script.


>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.74 do SDK do Azure Machine Learning.

## <a name="compute-targets-for-training"></a>Destinos de computação para treinamento

O Azure Machine Learning tem suporte variado nos diferentes destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou realiza treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning clusters de computação podem ser criados como um recurso persistente ou criados dinamicamente quando você solicita uma execução. A criação baseada em execução remove o destino de computação após a conclusão da execução de treinamento, portanto, não é possível reutilizar os destinos de computação criados dessa maneira.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Durante o treinamento, é comum iniciar em seu computador local e depois executar esse script de treinamento em um destino de computação diferentes. Com o Azure Machine Learning, você pode executar seu script em vários destinos de computação sem precisar alterar seu script.

Tudo que você precisa fazer é definir o ambiente para cada destino de computação com uma **configuração de execução**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para esse tipo de computação. Para obter detalhes sobre como especificar um ambiente e associá-lo para executar a configuração, consulte [Criar e gerenciar ambientes de treinamento e implantação](how-to-use-environments.md).

Saiba mais sobre [enviar experimentos](#submit) no final deste artigo.

## <a name="whats-an-estimator"></a>O que é um estimador?

Para facilitar o treinamento do modelo usando estruturas populares, o SDK do Python do Azure Machine Learning oferece uma abstração alternativa de nível mais alto, a classe do estimador.  Essa classe permite que você construa configurações de execução facilmente. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico de para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como Scikit-learn). É recomendável usar um estimador para treinamento, pois ele cria objetos inseridos automaticamente para você, como um ambiente ou objetos RunConfiguration. Caso queira ter mais controle sobre como esses objetos são criados e especificar quais pacotes instalar para a execução do seu experimento, siga [essas etapas](#amlcompute) para enviar seus experimentos de treinamento usando um objeto RunConfiguration em uma Computação do Azure Machine Learning.

O Azure Machine Learning fornece estimadores específicos para [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) e [Ray RLlib](how-to-use-reinforcement-learning.md).

Para obter mais informações, consulte [Treinar modelos de ML com os estimadores](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>O que é um pipeline de ML?

Com pipelines de ML, você pode otimizar o fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com o Azure Machine Learning, você pode se concentrar em suas competências, o aprendizado de máquina, em vez de na infraestrutura e na automação.

Os pipelines de ML são construídos a partir de várias **etapas**, que são unidades computacionais distintas no pipeline. Cada etapa pode ser executada de forma independente e usar recursos de computação isolados. Essa abordagem permite que vários cientistas de dados trabalhem no mesmo pipeline ao mesmo tempo sem sobrecarregar os recursos de computação, além de facilitar o uso de diferentes tipos/tamanhos de computação para cada etapa.

> [!TIP]
> Os pipelines de ML podem usar a configuração de execução ou os estimadores ao treinar modelos.

Embora os pipelines de ML possam treinar modelos, eles também podem preparar dados antes de treinar e implantar modelos após o treinamento. Um dos principais casos de uso para pipelines é a pontuação em lote. Para obter mais informações, consulte: [Pipelines: fluxos de trabalho de aprendizado de máquina de otimizar](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Configurar no Python

Use as seções a seguir para configurar estes destinos de computação:

* [Computador local](#local)
* [Cluster de computação do Azure Machine Learning](#amlcompute)
* [Instância de computação do Azure Machine Learning](#instance)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Computador local

1. **Criar e anexar**: Não é necessário criar nem anexar um destino de computação para usar seu computador local como o ambiente de treinamento.  

1. **Configurar**:  Quando você usa seu computador local como um destino de computação, o código de treinamento é executado em seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes do Python de que você precisa, use o ambiente gerenciado pelo usuário.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning cluster de computação

Azure Machine Learning cluster de computação é uma infraestrutura de computação gerenciada que permite criar facilmente uma computação de vários nós ou um único nó. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma Rede Virtual do Azure. A computação é executada em um ambiente em contêineres, empacotando as dependências do modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

É possível usar a Computação do Azure Machine Learning para distribuir o processo de treinamento em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](how-to-manage-quotas.md).


> [!TIP]
> Os clusters geralmente podem ser escalados verticalmente para até 100 nós, desde que você tenha cota suficiente para o número de núcleos necessários. Por padrão, os clusters são configurados com a comunicação entre nós habilitada entre os nós do cluster para dar suporte a trabalhos de MPI, por exemplo. No entanto, você pode dimensionar seus clusters para milhares de nós simplesmente [gerando um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)e solicitando que o liste sua assinatura, ou espaço de trabalho, ou um cluster específico para desabilitar a comunicação entre nós. 

Uma Computação do Azure Machine Learning pode ser reutilizada entre execuções. A computação pode ser compartilhada com outros usuários no espaço de trabalho e é mantida entre as execuções, escalando ou reduzindo vertical e automaticamente os nós com base no número de execuções enviadas e o max_nodes definido no cluster. A configuração min_nodes controla os nós mínimos disponíveis.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Criar e anexar**: Para criar um recurso persistente de Computação do Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. A computação é reduzida automaticamente até zero nó quando não é usada.   VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
    * **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
    * **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.  Veja [AmlCompute classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter detalhes.

    Outra opção é criar e anexar a um recurso persistente de Computação do Azure Machine Learning no [estúdio do Azure Machine Learning](#portal-create).

   
1. **Configurar**: Crie uma configuração de execução para o destino de computação persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Reduzir o custo do cluster de computação

Você também pode optar por usar [VMs de baixa prioridade](concept-plan-manage-cost.md#low-pri-vm) para executar algumas ou todas as suas cargas de trabalho. Essas VMs não têm disponibilidade garantida e podem ser substituídas enquanto estiverem em uso. Um trabalho admitido é reiniciado, não retomado. 

Use qualquer uma dessas maneiras para especificar uma VM de baixa prioridade:
    
* No estúdio, escolha **baixa prioridade** ao criar uma VM.
    
* Com o SDK do Python, defina o `vm_priority` atributo em sua configuração de provisionamento.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Usando a CLI, defina `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurar identidade gerenciada

 Azure Machine Learning clusters de computação também oferecem suporte a [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o acesso aos recursos do Azure sem incluir credenciais em seu código. Há dois tipos de identidades gerenciadas:

* Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente no cluster de computação Azure Machine Learning. O ciclo de vida de uma identidade atribuída pelo sistema está diretamente ligado ao cluster de computação. Se o cluster de computação for excluído, o Azure limpará automaticamente as credenciais e a identidade no Azure AD.
* Uma **identidade gerenciada atribuída pelo usuário** é um recurso autônomo do Azure fornecido por meio do serviço de identidade gerenciada do Azure. Você pode atribuir uma identidade gerenciada atribuída pelo usuário a vários recursos e ela persiste pelo tempo desejado.

Use qualquer uma dessas maneiras para especificar uma identidade gerenciada para um cluster de computação:
    
* No estúdio, quando a criação do cluster de computação ou ao editar detalhes do cluster de computação, alterne **a opção atribuir uma identidade gerenciada** e especifique uma identidade atribuída pelo sistema ou identidade atribuída pelo usuário.
    
* Com o SDK do Python, defina o `identity_type` atributo em sua configuração de provisionamento.  
    
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )

    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

* Com o SDK do Python, defina `identity_type` os `identity_id` atributos e (se estiver criando uma identidade gerenciada atribuída pelo usuário) em sua configuração de provisionamento.  
    
    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")

    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```
    
* Usando a CLI, defina o `assign-identity` atributo durante a criação do cluster:
    
    ```azurecli
    # create a cluster with a user-assigned managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # create a cluster with a system-managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'

* Using the CLI, execute the following commands to assign a managed identity on an existing cluster:
    
    ```azurecli
    # add a user-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # add a system-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'

> [!NOTE]
> Azure Machine Learning compute clusters support only **one system-assigned identity** or **multiple user-assigned identities**, not both concurrently.
> 
> Additionally, you can assign only one managed identity from the studio.

#### Managed identity usage

AML defines the **default managed identity** as the system-assigned managed identity or the first user-assigned managed identity.

During a run there are two applications of an identity:
1. The system uses an identity to setup the user's storage mounts, container registry, and datastores.
    * In this case, the system will use the default managed identity.

1. The user applies an identity to access resources from within the code for a submitted run
    
    * In this case, the user must provide the *client_id* corresponding to the managed identity they want to use to retrieve a credential. 
    * Alternatively, AML exposes the user-assigned identity's client ID through the *DEFAULT_IDENTITY_CLIENT_ID* environment variable.
    
    For example, to retrieve a token for a datastore with the default managed identity:
    
    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')



### <a id="instance"></a>Azure Machine Learning compute instance

[Azure Machine Learning compute instance](concept-compute-instance.md) is a managed-compute infrastructure that allows you to easily create a single VM. The compute is created within your workspace region, but unlike a compute cluster, an instance cannot be shared with other users in your workspace. Also the instance does not automatically scale down.  You must stop the resource to prevent ongoing charges.

A compute instance can run multiple jobs in parallel and has a job queue. 

Compute instances can run jobs securely in a [virtual network environment](how-to-enable-virtual-network.md#compute-instance), without requiring enterprises to open up SSH ports. The job executes in a containerized environment and packages your model dependencies in a Docker container. 

1. **Create and attach**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

1. **Configurar**: criar uma configuração de execução.
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.

Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

Use o Máquina Virtual de Ciência de Dados do Azure (DSVM) como a VM do Azure escolhida para esse cenário. Essa VM é uma ciência de dados pré-configuradas e o ambiente de desenvolvimento de inteligência artificial do Azure. A VM oferece uma opção selecionada de ferramentas e estruturas para desenvolvimento do aprendizado de máquina de todo o ciclo de vida. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Criar**: Crie uma DSVM antes de usá-la para treinar seu modelo. Para criar esse recurso, veja [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning só dá suporte a máquinas virtuais que executam o **Ubuntu**. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.
    > 
    > Azure Machine Learning também exige que a máquina virtual tenha um __endereço IP público__.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer a ID do recurso, o nome de usuário e a senha da máquina virtual. A ID de recurso da VM pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome da VM com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar a DSVM ao seu workspace [usando o estúdio do Azure Machine Learning](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning requer que o cluster HDInsight tenha um __endereço IP público__.

    Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
    
    Depois que o cluster for criado, conecte-o com o hostname \<clustername> -SSH.azurehdinsight.net, em que \<clustername> é o nome que você forneceu para o cluster. 

1. **Anexar**: Para anexar a um cluster do HDInsight como um destino de computação, forneça a ID do recurso, o nome de usuário e a senha para o cluster HDInsight. A ID de recurso do cluster HDInsight pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome do cluster HDInsight com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar o cluster HDInsight ao seu workspace [usando o estúdio do Azure Machine Learning](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Lote do Azure 

O Lote do Azure é usado para executar com eficiência aplicativos HPC (computação de alto desempenho) e paralelos em grande escala na nuvem. O AzureBatchStep pode ser usado em um Pipeline do Azure Machine Learning para enviar trabalhos a um pool de computadores do Lote do Azure.

Para anexar o Lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e fornecer as seguintes informações:

-    **Nome de computação do Lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-    **Nome da conta do Lote do Azure**: O nome da conta do Lote do Azure
-    **Grupo de Recursos**: O grupo de recursos que contém o a conta do Lote do Azure.

O código a seguir demonstra como anexar o Lote do Azure como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie um workspace do Azure Databricks antes de usá-lo. Para criar um recurso de espaço de trabalho, consulte o documento [executar um trabalho do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Para anexar o Azure Databricks como um destino de computação, forneça as seguintes informações:

* __Nome de computação do databricks__: o nome que você deseja atribuir a esse recurso de computação.
* __Nome do espaço de trabalho do databricks__: o nome do espaço de trabalho Azure Databricks.
* __Token de acesso do databricks__: o token de acesso usado para autenticar para Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

O código a seguir demonstra como anexar Azure Databricks como um destino de computação com o SDK do Azure Machine Learning (__o espaço de trabalho do databricks precisa estar presente na mesma assinatura que o seu espaço de trabalho AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-databricks) no github.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Análise Azure Data Lake

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de usá-lo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome da computa__: o nome que você deseja atribuir a este recurso de computação.
* __Grupo de recursos__: o grupo de recursos que contém a conta de data Lake Analytics.
* __Nome da conta__: o nome da conta de data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-adla) no github.

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados com os quais você precisa trabalhar estiverem em um repositório não padrão, você poderá usar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes do treinamento.

## <a name="set-up-in-azure-machine-learning-studio"></a>Configurar o estúdio do Azure Machine Learning

Você pode acessar quais destinos de computação são associados com seu workspace no estúdio do Azure Machine Learning.  Você pode usar o estúdio para:

* [Exibir destinos de computação](#portal-view) anexado ao seu workspace
* [Criar um destino de computação](#portal-create) no workspace
* [Anexar um destino de computação](#portal-reuse) que foi criado fora do workspace


Depois que um destino é criado e anexado ao seu workspace, você o usará em sua configuração de execução com um objeto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Exibir destinos de computação


Para ver os destinos de computação do seu workspace, use as seguintes etapas:

1. Link para o [estúdio do Azure Machine Learning](https://ml.azure.com).
 
1. Em __Aplicativos__, selecione __Computação__.

    [![Guia Exibir computação](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Criar um destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Insira um nome para o destino de computação. 

1. Selecione **Computação do Machine Learning** como o tipo da computação a ser usada para __Treinamento__. 

    >[!NOTE]
    >A Computação do Azure Machine Learning é o único recurso de computação gerenciado que você pode criar no estúdio do Azure Machine Learning.  Todos os outros recursos de computação podem ser anexados depois de serem criados.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente **Família de VMs**e o **máximo de nós** utilizado para acelerar a computação.  

1. Selecione __Criar__.


1. Exiba o status da operação de criação, selecionando o destino de computação na lista:

    ![Selecione um destino de computação para exibir o status da operação criar](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, visualize os detalhes do destino de computação: 

    ![Exibir os detalhes do destino de computação](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Anexar destinos de computação

Para usar destinos de computação criados fora do workspace do Azure Machine Learning, é necessário anexá-los. Anexar um destino de computação o disponibiliza para seu workspace.

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use as etapas abaixo para anexar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação. 
1. Insira um nome para o destino de computação. 
1. Selecione o tipo de computação a ser anexado para __Treinamento__:

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados no estúdio do Azure Machine Learning. Os tipos de computação que podem ser anexados para treinamento são:
    >
    > * VM remota
    > * Azure Databricks (para uso em pipelines de aprendizado de máquina)
    > * Azure Data Lake Analytics (para uso em pipelines de aprendizado de máquina)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que as senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH contam com as assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecionar __Anexar__. 
1. Exiba o status da operação de anexação, selecionando o destino de computação na lista.

## <a name="set-up-with-cli"></a>Configurar com a CLI

Você pode acessar os destinos de computação associados ao seu workspace usando a [extensão da CLI](reference-azure-machine-learning-cli.md) para o Azure Machine Learning.  Você pode usar a CLI para:

* Criar um destino de computação gerenciado
* Atualizar um destino de computação gerenciado
* Anexar um destino de computação não gerenciado

Para obter mais informações, veja [Gerenciamento de recursos](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurar com o VS Code

Você pode acessar, criar e gerenciar os destinos de computação associados ao seu workspace usando a [extensão do VS Code](how-to-manage-resources-vscode.md#compute-clusters) do Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Enviar a execução de treinamento usando o SDK do Azure Machine Learning

Depois de criar uma configuração de execução, você pode usá-la para executar seu teste.  O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Enviar a execução.
1. Aguarde a conclusão da execução.

> [!IMPORTANT]
> Ao enviar a execução de treinamento, um instantâneo do diretório contendo os scripts de treinamento será criado e enviado ao destino de computação. Ele também é armazenado como parte do experimento em seu workspace. Se você alterar os arquivos e enviar a execução novamente, apenas os arquivos alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para obter mais informações, veja [cópias de sombra](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Criar uma experiência

Primeiro, crie um experimento no seu workspace.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Enviar o teste

Envie o experimento com um objeto `ScriptRunConfig`.  Esse objeto inclui:

* **source_directory**: o diretório de origem que contém o script de treinamento
* **script**: identificar o script de treinamento
* **run_config**: a configuração de execução que, por sua vez, define em que local ocorrerá o treinamento.

Por exemplo, para usar a configuração de [destino local](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Alterne o mesmo experimento para ser executado em um destino de computação diferente usando uma configuração de execução diferente, como [destino amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Este exemplo tem como padrão usar apenas um nó do destino de computação para treinamento. Para usar mais de um nó, defina o `node_count` da configuração de execução para o número desejado de nós. Por exemplo, o código a seguir define o número de nós usados para treinamento como quatro:
>
> ```python
> src.run_config.node_count = 4
> ```

Ou você pode:

* Enviar o teste com um objeto `Estimator` conforme mostrado em [Treinar modelos de ML com estimadores](how-to-train-ml-models.md).
* Envie uma execução de HyperDrive para [ajustar o hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie um experimento por meio da [extensão do VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Para obter mais informações, consulte a documentação de [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py).

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Criar a configuração de execução e enviar a execução usando a CLI do Azure Machine Learning

Você pode usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e a [extensão da CLI do Machine Learning](reference-azure-machine-learning-cli.md) para criar configurações de execução e enviar execuções em diferentes destinos de computação. Os exemplos a seguir pressupõem que você tenha um Workspace do Azure Machine Learning existente e que tenha feito logon no Azure usando o comando `az login` da CLI. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Criar configuração de execução

A maneira mais simples de criar a configuração de execução é navegar na pasta que contém seus scripts do Python do aprendizado de máquina e usar o comando da CLI

```azurecli
az ml folder attach
```

Esse comando cria uma subpasta `.azureml` que contém arquivos de configuração de execução de modelo para destinos de computação diferentes. Você pode copiar e editar esses arquivos para personalizar sua configuração para adicionar pacotes do Python ou alterar as configurações do Docker, por exemplo.  

### <a name="structure-of-run-configuration-file"></a>Estrutura do arquivo de configuração de execução

O arquivo de configuração de execução é um YAML formatado e com as seções a seguir
 * O script a ser executado e seus argumentos
 * Nome do destino de computação, como “local” ou nome de uma computação no workspace.
 * Parâmetros para a execução: estrutura, comunicador para execuções distribuídas, duração máxima e número de nós de computação.
 * Seção de ambiente. Consulte [Criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md) para obter detalhes dos campos nessa seção.
   * Para especificar os pacotes do Python a serem instalados para a execução, crie um [arquivo de ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e defina o campo __condaDependenciesFile__.
 * Detalhes do histórico de execuções para especificar a pasta do arquivo de log e para habilitar ou desabilitar a coleta de saída e os instantâneos de histórico de execuções.
 * Detalhes de configuração específicos para a estrutura selecionada.
 * Referência de dados e detalhes do armazenamento de dados.
 * Detalhes de configuração específicos para a Computação do Machine Learning para criar um novo cluster.

Consulte o exemplo [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) para obter um esquema runconfig completo.

### <a name="create-an-experiment"></a>Criar uma experiência

Primeiro, crie um experimento para suas execuções

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Execução de script

Para enviar uma execução de script, execute um comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Execução de HyperDrive

Você pode usar o HyperDrive com a CLI do Azure para realizar as execuções de ajuste de parâmetro. Primeiro, crie um arquivo de configuração HyperDrive no formato a seguir. Consulte o artigo [Ajustar hiperparâmetros para seu modelo](how-to-tune-hyperparameters.md) para obter detalhes sobre parâmetros de ajuste de hiperparâmetro.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adicione esse arquivo junto com os arquivos de configuração de execução. Em seguida, envie uma execução de HyperDrive usando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observe a seção *argumentos*  no runconfig e o *espaço de parâmetro* na configuração do HyperDrive. Eles contêm os argumentos de linha de comando a serem passados ao script de treinamento. O valor em runconfig permanece o mesmo em cada iteração, enquanto o intervalo na configuração de HyperDrive é iterado. Não especifique o mesmo argumento em ambos os arquivos.

Para obter mais detalhes sobre esses comandos ```az ml``` da CLI, consulte [a documentação de referência](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Acompanhamento e integração do Git

Quando você inicia uma execução de treinamento em que o diretório de origem é um repositório Git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, confira [Integração do Git com o Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar hiperparâmetros de modo eficiente](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SKD da [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](how-to-enable-virtual-network.md)
