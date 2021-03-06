---
title: 'Início Rápido: Criar um cluster Spark no HDInsight usando o portal do Azure'
description: Este início rápido mostra como usar o portal do Azure para criar um cluster Apache Spark no Azure HDInsight e executar uma consulta Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 1d816a84dc8062890633661716cf78aa5ba58527
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888855"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Início Rápido: Criar um cluster do Apache Spark no Azure HDInsight usando o portal do Azure

Neste início rápido, você usará o portal do Azure para criar um cluster Apache Spark do Azure HDInsight. Em seguida, você criará um Jupyter notebook e o usará para executar consultas SQL do Spark em tabelas do Apache Hive. O Azure HDInsight é um serviço de análise de software livre gerenciado e de amplo espectro para empresas. A estrutura Apache Spark para HDInsight permite análises rápidas de dados e computação de cluster usando o processamento na memória. O Jupyter notebook permite que você interaja com seus dados, combine o código com texto markdown e faça visualizações simples.

Para obter explicações detalhadas sobre as configurações disponíveis, confira [Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre o uso do portal para criar clusters, confira [Criar clusters no portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Se você estiver usando vários clusters juntos, desejará criar uma rede virtual e, se estiver usando um cluster do Spark, também desejará usar o Hive Warehouse Connector. Para obter mais informações, confira [Planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) e [Integrar o Apache Spark e o Apache Hive com o Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

> [!IMPORTANT]  
> A cobrança dos clusters do HDInsight será proporcional por minuto, independentemente de eles estarem sendo usados ou não. Exclua seu cluster depois de terminar de usá-lo. Para saber mais, confira a seção [Recursos de limpeza](#clean-up-resources) deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Criar um cluster Apache Spark no HDInsight

Você usará o portal do Azure para criar um cluster HDInsight que usa blobs do Armazenamento do Azure como o armazenamento de cluster. Para obter mais informações sobre como usar o Data Lake Storage Gen2, consulte o [Guia de Início Rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No menu superior, selecione **+ Criar um recurso**.

    ![Criar um recurso no portal do Azure](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Criar um recurso no Portal do Azure")

1. Selecione **Análise** > **Azure HDInsight** para acessar a página **Criar cluster HDInsight**.

1. Na guia **Informações Básicas**, forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscription  | Na lista suspensa, selecione a assinatura do Azure usada para o cluster. |
    |Resource group | Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
    |Nome do cluster | Insira um nome global exclusivo.|
    |Região   | Na lista suspensa, selecione uma região em que o cluster foi criado. |
    |Tipo de cluster| Escolha Selecionar tipo de cluster para abrir uma lista. Na lista, selecione **Spark**.|
    |Versão do cluster|Esse campo será preenchido automaticamente com a versão padrão depois que o tipo de cluster tiver sido selecionado.|
    |Nome de usuário de logon do cluster| Insira o nome de logon do usuário do cluster.  O nome padrão é **admin**. Você pode usar essa conta fazer logon no bloco de anotações do Jupyter mais tarde no início rápido. |
    |Senha de logon do cluster| Insira a senha de logon do cluster. |
    |Nome de usuário do Secure Shell (SSH)| Insira um Nome de Usuário SSH. O nome de usuário de SSH usado para este início rápido é **sshuser**. Por padrão, essa conta tem a mesma senha que a conta de*nome de usuário de logon do cluster*. |

    ![Criar configurações básicas do cluster HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Criar cluster Spark no HDInsight com as Configurações básicas")

    Selecione **Avançar: Armazenamento >>** para continuar para a página **Armazenamento**.

1. Em **Armazenamento**, forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Use o valor padrão **Armazenamento do Azure**.|
    |Método de seleção|Use o valor padrão **Selecione na lista**.|
    |Conta de armazenamento primária|Use o valor preenchido automaticamente.|
    |Contêiner|Use o valor preenchido automaticamente.|

    ![Criar configurações básicas do cluster HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Criar cluster Spark no HDInsight com as Configurações básicas")

    Selecione **Examinar + criar** para continuar.

1. Em **Examinar + criar**, selecione **Criar**. Demora cerca de 20 minutos para criar o cluster. O cluster deve ser criado antes de prosseguir para a próxima sessão.

Se você tiver um problema com a criação de clusters HDInsight, talvez não tenha as permissões corretas para fazer isso. Para saber mais, confira [Requisitos do controle de acesso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Criar um notebook Jupyter

O Jupyter Notebook é um ambiente de notebook interativo que oferece suporte a várias linguagens de programação. O notebook permite que você interaja com seus dados, combine código com texto markdown e execute visualizações simples.

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter`, em que `CLUSTERNAME` é o nome do cluster. Em caso de solicitação, insira as credenciais de logon do cluster.

1. Selecione **Novo** > **PySpark** para criar um notebook.

   ![Criar um Jupyter Notebook para executar consultas SQL interativas do Spark](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Criar um Jupyter Notebook para executar consultas SQL interativas do Spark")

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar instruções SQL do Apache Spark

O SQL (Structured Query Language) é a linguagem mais comum e amplamente usada para consultar e definição de dados. O Spark SQL funciona como uma extensão do Apache Spark para processar dados estruturados, usando a sintaxe SQL familiar.

1. Verifique se o kernel está pronto. O kernel estará pronto quando você vir um círculo vazio ao lado do nome do kernel no notebook. Círculo sólido indica que o kernel está ocupado.

    ![Consulta do Apache Hive no HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Consulta do Hive no HDInsight")

    Quando você inicia o notebook pela primeira vez, o kernel executa algumas tarefas em segundo plano. Aguarde a leitura do kernel.

1. Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. O comando lista as tabelas de Hive no cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Quando você usa um Jupyter Notebook com o cluster HDInsight, obtém uma predefinição `sqlContext` que pode ser usada para executar consultas do Hive usando o Spark SQL. `%%sql` informa ao Notebook Jupyter para usar a predefinição `sqlContext` para executar a consulta Hive. A consulta recupera as primeiras 10 linhas de uma tabela Hive (**hivesampletable**) que vem com todos os clusters HDInsight por padrão. Ele leva aproximadamente 30 segundos para obter os resultados. A saída se parece com isso:

    ![Consulta do Apache Hive no HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Consulta do Hive no HDInsight")

    Toda vez que você executar uma consulta no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados em `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A tela deve ser atualizada para mostrar a saída da consulta.

    ![Resultado da consulta do Hive no HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Resultado da consulta do Hive no HDInsight")

1. No menu **Arquivo** do notebook, selecione **Fechar e Interromper**. O desligamento do bloco de anotações libera os recursos do cluster.

## <a name="clean-up-resources"></a>Limpar os recursos

O HDInsight salva seus dados no Armazenamento do Azure ou no Azure Data Lake Storage, de modo que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Se você planeja trabalhar no tutorial listado em [Próximas etapas](#next-steps) imediatamente, convém manter o cluster.

Retorne ao Portal do Azure e selecione **Excluir**.

![Excluir um cluster do HDInsight no portal do Azure](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "Excluir cluster HDInsight")

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster Apache Spark no HDInsight e executar uma consulta SQL básica do Spark. Avance para o próximo tutorial para saber como usar um cluster HDInsight para executar consultas interativas em dados de exemplo.

> [!div class="nextstepaction"]
> [Executar consultas interativas no Apache Spark](./apache-spark-load-data-run-query.md)
