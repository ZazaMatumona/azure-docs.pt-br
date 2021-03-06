---
title: Visão geral da segurança empresarial no Azure HDInsight
description: Conheça os vários métodos para garantir a segurança empresarial no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 1869671b465b7175cf3160c41debc66cbd0818ad
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367097"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Visão geral da segurança empresarial no Azure HDInsight

O Azure HDInsight oferece vários métodos para atender às suas necessidades de segurança empresarial. A maioria dessas soluções não está ativada por padrão. Essa flexibilidade permite que você escolha os recursos de segurança que são mais importantes para você e ajuda a evitar o pagamento de recursos indesejados. Essa flexibilidade também significa que é sua responsabilidade verificar se as soluções corretas estão habilitadas para sua instalação e seu ambiente.

Este artigo analisa as soluções de segurança dividindo-as em quatro pilares de segurança tradicionais: segurança de perímetro, autenticação, autorização e criptografia.

Este artigo também apresenta o **ESP (Enterprise Security Package) do Azure HDInsight**, que fornece autenticação baseada no Active Directory, suporte multiusuário e controle de acesso baseado em função para clusters HDInsight.

## <a name="enterprise-security-pillars"></a>Pilares da segurança empresarial

Uma maneira de analisar a segurança empresarial divide as soluções de segurança em quatro grupos principais com base no tipo de controle. Esses grupos também são chamados de pilares de segurança e são os seguintes tipos: segurança de perímetro, autenticação, autorização e criptografia.

### <a name="perimeter-security"></a>Segurança de perímetro

A segurança de perímetro no HDInsight é obtida por meio de [redes virtuais](../hdinsight-plan-virtual-network-deployment.md). Um admin corporativo pode criar um cluster dentro de uma VNET (rede virtual) e usar NSGs (grupos de segurança de rede) para restringir o acesso à rede virtual. Somente os endereços IP permitidos nas regras NSG de entrada podem se comunicar com o cluster HDInsight. Essa configuração fornece segurança de perímetro.

Todos os clusters implantados em uma VNET também terão um ponto de extremidade privado. O ponto de extremidade é resolvido para um IP privado dentro da VNET para acesso HTTP privado aos gateways de cluster.

### <a name="authentication"></a>Autenticação

O [Enterprise Security Package](apache-domain-joined-architecture.md) do HDInsight fornece autenticação baseada no Active Directory, suporte multiusuário e controle de acesso baseado em função. A integração do Active Directory é obtida pelo uso do [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Com essas funcionalidades, você poderá criar um cluster HDInsight que ingressado em um domínio do Active Directory. Em seguida, configure uma lista de funcionários da empresa que podem se autenticar no cluster.

Com essa configuração, funcionários da empresa podem entrar nos nós de cluster, usando as credenciais de domínio. Eles também podem usar as credenciais de domínio para autenticação com outros pontos de extremidade aprovados. Como Apache Ambari Views, ODBC, JDBC, PowerShell e APIs REST para interagir com o cluster.

### <a name="authorization"></a>Autorização

Uma melhor prática que a maioria das empresas segue é garantir que nem todos os funcionários tenham acesso completo a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controle de acesso baseadas em função para os recursos do cluster. Essa ação só está disponível nos clusters ESP.

O administrador do Hadoop pode configurar o RBAC (controle de acesso baseado em função). As configurações protegem o Apache [Hive](apache-domain-joined-run-hive.md), o [HBase](apache-domain-joined-run-hbase.md) e o [Kafka](apache-domain-joined-run-kafka.md) com plug-ins do Apache Ranger. A configuração de políticas de RBAC permite que você associe permissões a uma função na organização. Essa camada de abstração facilita garantir que as pessoas tenham apenas as permissões necessárias para realizar suas responsabilidades de trabalho. O Ranger também permite auditar o acesso a dados de funcionários e as alterações feitas nas políticas de controle de acesso.

Por exemplo, o administrador pode configurar o [Apache Ranger](https://ranger.apache.org/) para definir políticas de controle de acesso para o Hive. Essa funcionalidade garante a filtragem em nível de linha e de coluna (mascaramento de dados). E filtra os dados confidenciais de usuários não autorizados.

### <a name="auditing"></a>Auditoria

O acesso a recursos de cluster de auditoria é necessário para controlar o acesso não autorizado ou não intencional aos recursos. Também é muito importante proteger os recursos do cluster contra o acesso não autorizado.

O administrador pode exibir e relatar todo o acesso aos recursos e dados de cluster do HDInsight. O administrador pode visualizar e gerar relatórios sobre as alterações nas políticas de controle de acesso.

Para acessar os logs de auditoria do Apache Ranger e do Ambari, bem como os logs de acesso ao SSH, [habilite o Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) e veja as tabelas que fornecem registros de auditoria.

### <a name="encryption"></a>Criptografia

A proteção de dados é importante para atender aos requisitos de segurança e conformidade da organização. Além de restringir o acesso a dados de funcionários não autorizados, você deve criptografá-lo.

O armazenamento do Azure e o Data Lake Storage Gen1/Gen2 são compatíveis com [criptografia de dados](../../storage/common/storage-service-encryption.md) em repouso transparente no lado do servidor. A proteção de clusters HDInsight funcionará perfeitamente com essa criptografia do lado do servidor de dados em repouso.

### <a name="compliance"></a>Conformidade

As ofertas de conformidade do Azure baseiam-se em vários tipos de garantia, incluindo certificações formais. Além disso, atestado, validações e autorizações. Avaliações produzidas por empresas independentes de auditoria de terceiros. Documentos de emendas contratuais, autoavaliações e orientação do cliente produzidos pela Microsoft. Para obter informações sobre a conformidade do HDInsight, confira a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trust-center) e a [Visão geral da conformidade no Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade compartilhada

A imagem a seguir resume as principais áreas de segurança do sistema e as soluções de segurança que estão disponíveis para você em cada uma delas. Ele também realça quais áreas de segurança são de sua responsabilidade como um cliente. E quais áreas são de responsabilidade do HDInsight como o provedor de serviços.

![Diagrama de responsabilidades compartilhadas do HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

A tabela a seguir fornece links para recursos para cada tipo de solução de segurança.

| Área de segurança | Soluções disponíveis | Parte responsável |
|---|---|---|
| Segurança de acesso a dados | Configurar as [ACLs (listas de controle de acesso)](../../storage/blobs/data-lake-storage-access-control.md) para o Azure Data Lake Storage Gen1 e Gen2  | Cliente |
|  | Habilitar a propriedade ["Transferência segura obrigatória"](../../storage/common/storage-require-secure-transfer.md) nas contas de armazenamento. | Cliente |
|  | Configurar redes virtuais e [firewalls do Armazenamento do Azure](../../storage/common/storage-network-security.md) | Cliente |
|  | Configurar [pontos de extremidade de serviço de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) para Cosmos DB e [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Cliente |
|  | Verificar se a [criptografia TLS](../../storage/common/storage-security-tls.md) está habilitada para os dados em trânsito. | Cliente |
|  | Configurar as [chaves gerenciadas pelo cliente](../../storage/common/storage-encryption-keys-portal.md) para a criptografia do Armazenamento do Azure | Cliente |
|  | Controlar o acesso aos seus dados pelo Suporte do Azure usando o [Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview) | Cliente |
| Segurança de aplicativo e middleware | Fazer a integração ao AAD-DS e [configurar a autenticação](apache-domain-joined-configure-using-azure-adds.md) | Cliente |
|  | Configurar políticas de [Autorização do Apache Ranger](apache-domain-joined-run-hive.md) | Cliente |
|  | Usar os [logs do Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Cliente |
| Segurança do sistema operacional | Criar clusters com a imagem base segura mais recente | Cliente |
|  | Garantir a [aplicação de patch do sistema operacional](../hdinsight-os-patching.md) em intervalos regulares | Cliente |
| Segurança de rede | Configurar uma [rede virtual](../hdinsight-plan-virtual-network-deployment.md) |
|  | Configurar as [regras NSG (grupo de segurança de rede) de entrada](../control-network-traffic.md) | Cliente |
|  | Configurar a [Restrição de tráfego de saída](../hdinsight-restrict-outbound-traffic.md) com o firewall | Cliente |
| Infraestrutura virtualizada | N/D | HDInsight (provedor de nuvem) |
| Segurança de infraestrutura física | N/D | HDInsight (provedor de nuvem) |

## <a name="next-steps"></a>Próximas etapas

* [Planejar clusters do HDInsight com ESP](apache-domain-joined-architecture.md)
* [Configurar clusters do HDInsight com ESP](apache-domain-joined-configure.md)
* [Gerenciar clusters do HDInsight com ESP](apache-domain-joined-manage.md)
