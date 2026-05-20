# Azure Monitor Workbooks — Insights de Plataforma

Coleção de workbooks do Azure Monitor focados em **métricas nativas** (Azure Monitor Metrics), **sem uso de Log Analytics / Workspace queries**.

---

## 📦 Workbooks Disponíveis

### `azure-monitor-insights-platform-workbook.json`

Workbook consolidado para monitoramento de consumo e utilização de recursos Azure com janela temporal de **7 dias ou mais**.

**Cobertura:**
- 💻 Máquinas Virtuais (CPU, disco, rede)
- 🌐 App Services (CPU, memória, requests, latência, erros HTTP)
- 🗄️ Storage Accounts (transações, ingress/egress, disponibilidade, latência, capacidade)
- 🛢️ Databases PaaS (Azure SQL, PostgreSQL Flexible, MySQL Flexible, Cosmos DB)
- 💡 Oportunidades de eficiência e right-sizing

---

## 🎯 Propósito

Oferecer uma visão consolidada de consumo e utilização de recursos Azure utilizando **exclusivamente Azure Monitor Metrics** e **Azure Resource Graph**, sem dependência de Log Analytics Workspace. Isso garante:

- Funcionalidade mesmo em ambientes sem workspace configurado
- Menor custo (sem ingestão de logs para este fim)
- Menor latência nas métricas (métricas nativas são coletadas automaticamente)
- Visão gerencial e técnica em um único workbook

---

## 🏗️ Estrutura do Workbook

O workbook é organizado em **6 abas**:

| Aba | Descrição |
|-----|-----------|
| 📊 **Executivo** | Visão geral de inventário por tipo de recurso e distribuição geográfica |
| 💻 **Máquinas Virtuais** | CPU (avg/max), disco OS e Data (bytes/s, IOPS), rede (in/out) |
| 🌐 **App Services** | CPU/Memória do App Service Plan, requests, erros HTTP, latência, tráfego |
| 🗄️ **Storage** | Transações, ingress/egress, disponibilidade, latência E2E/servidor, capacidade |
| 🛢️ **Databases PaaS** | Azure SQL, PostgreSQL Flex, MySQL Flex e Cosmos DB |
| 💡 **Oportunidades** | Inventário por SKU/tier com análise de potencial right-sizing |

### Parâmetros Globais

| Parâmetro | Tipo | Padrão | Descrição |
|-----------|------|--------|-----------|
| **Assinatura** | Subscription Picker | Todas | Selecione uma ou mais assinaturas |
| **Grupo de Recursos** | Dropdown (ARG) | Todos | Filtra por grupo de recursos |
| **Período** | Time Range Picker | Últimos 7 dias | Opções: 7d, 15d, 30d, 60d ou personalizado |

---

## ⚠️ Limitações Conhecidas

### Memória em Máquinas Virtuais

A métrica de memória (`Available Memory Bytes`) **não é coletada automaticamente** pelo Azure Monitor em VMs. Para habilitar:

1. Instale o **Azure Monitor Agent (AMA)** na VM
2. Crie uma **Data Collection Rule (DCR)** com Performance Counters
3. Configure o **destino** da DCR como **Azure Monitor Metrics** (não Log Analytics Workspace)
4. Após configuração, acesse a aba **Métricas Virtuais** e filtre pelo namespace:
   - Windows: `azure.vm.windows.guestmetrics`
   - Linux: `azure.vm.linux.guestmetrics`

> Esta limitação é inerente ao Azure Monitor. O workbook indica claramente onde essa restrição se aplica.

### Métricas que variam por Provider

Algumas métricas podem não estar disponíveis dependendo do tier/SKU do recurso:
- `dtu_consumption_percent`: disponível apenas para Azure SQL no modelo DTU (não vCore)
- `NormalizedRUConsumption` (Cosmos DB): disponível apenas quando throughput provisionado está configurado
- Métricas de Blob/File/Queue/Table: requerem o subrecurso específico do Storage Account

---

## 📥 Como Importar o Workbook

### Via Portal do Azure

1. Acesse o **Azure Portal** → **Monitor** → **Workbooks**
2. Clique em **+ Novo** (ícone de lápis)
3. No editor, clique no ícone **`</>`** (modo avançado / JSON editor)
4. Apague o conteúdo existente e cole o conteúdo de `azure-monitor-insights-platform-workbook.json`
5. Clique em **✓ Aplicar** e depois em **💾 Salvar**
6. Preencha nome, assinatura e grupo de recursos onde deseja salvar o workbook

### Via Azure CLI

```bash
# Exemplo — salvar workbook em um Resource Group
az monitor workbook create \
  --resource-group <SEU_RESOURCE_GROUP> \
  --name "Azure Monitor Platform Insights" \
  --location <REGIAO> \
  --serialized-data @workbooks/azure-monitor-insights-platform-workbook.json \
  --kind shared
```

### Via Terraform / Bicep

O arquivo JSON pode ser referenciado como `serialized_data` em recursos do tipo `microsoft.insights/workbooks`.

---

## 📊 Fontes de Dados Utilizadas

| Fonte | Uso | Requer Log Analytics? |
|-------|-----|-----------------------|
| Azure Resource Graph (ARG) | Inventário, listagem e filtragem de recursos | ❌ Não |
| Azure Monitor Metrics | Todas as métricas de desempenho e consumo | ❌ Não |

> **Nenhuma consulta KQL de Log Analytics é utilizada neste workbook.**

---

## 🔍 Métricas por Tipo de Recurso

### Máquinas Virtuais (`microsoft.compute/virtualmachines`)

| Métrica | Agregação | Descrição |
|---------|-----------|-----------|
| `Percentage CPU` | Avg, Max | CPU do host em % |
| `OS Disk Read Bytes/sec` | Avg | Leitura do disco OS |
| `OS Disk Write Bytes/sec` | Avg | Escrita no disco OS |
| `OS Disk Read Operations/Sec` | Avg | IOPS de leitura OS |
| `OS Disk Write Operations/Sec` | Avg | IOPS de escrita OS |
| `Data Disk Read Bytes/sec` | Avg | Leitura dos discos de dados |
| `Data Disk Write Bytes/sec` | Avg | Escrita nos discos de dados |
| `Network In Total` | Total | Bytes recebidos |
| `Network Out Total` | Total | Bytes enviados |

### App Services (`microsoft.web/sites` e `microsoft.web/serverfarms`)

| Métrica | Namespace | Agregação |
|---------|-----------|-----------|
| `CpuPercentage` | serverfarms | Avg, Max |
| `MemoryPercentage` | serverfarms | Avg, Max |
| `MemoryWorkingSet` | sites | Avg |
| `AverageMemoryWorkingSet` | sites | Avg |
| `Requests` | sites | Total |
| `Http4xx` | sites | Total |
| `Http5xx` | sites | Total |
| `AverageResponseTime` | sites | Avg |
| `BytesReceived` / `BytesSent` | sites | Total |
| `AppConnections` / `Threads` | sites | Avg |

### Storage Accounts (`microsoft.storage/storageaccounts`)

| Métrica | Agregação |
|---------|-----------|
| `Transactions` | Total |
| `Ingress` / `Egress` | Total |
| `Availability` | Avg |
| `SuccessE2ELatency` | Avg |
| `SuccessServerLatency` | Avg |
| `UsedCapacity` | Avg |
| `BlobCapacity`, `BlobCount`, `ContainerCount` | Avg |

### Azure SQL Database (`microsoft.sql/servers/databases`)

| Métrica | Agregação |
|---------|-----------|
| `cpu_percent` | Avg |
| `dtu_consumption_percent` | Avg |
| `storage_percent` / `storage` | Avg |
| `sessions_percent` / `workers_percent` | Avg |
| `deadlock` | Total |
| `connection_successful` / `connection_failed` | Total |

### PostgreSQL Flexible Server (`microsoft.dbforpostgresql/flexibleservers`)

| Métrica | Agregação |
|---------|-----------|
| `cpu_percent` / `memory_percent` | Avg |
| `storage_percent` | Avg |
| `active_connections` | Avg |
| `connections_succeeded` / `connections_failed` | Total |
| `iops` | Avg |
| `network_bytes_ingress` / `network_bytes_egress` | Total |

### MySQL Flexible Server (`microsoft.dbformysql/flexibleservers`)

| Métrica | Agregação |
|---------|-----------|
| `cpu_percent` / `memory_percent` | Avg |
| `storage_percent` / `io_consumption_percent` | Avg |
| `active_connections` | Avg |
| `Queries` | Avg |
| `network_bytes_ingress` / `network_bytes_egress` | Total |

### Cosmos DB (`microsoft.documentdb/databaseaccounts`)

| Métrica | Agregação |
|---------|-----------|
| `TotalRequests` / `TotalRequestUnits` | Total |
| `NormalizedRUConsumption` | Max |
| `ServiceAvailability` | Avg |
| `ServerSideLatency` | Avg |
| `DataUsage` / `IndexUsage` / `DocumentCount` | Avg |

---

## 🤝 Contribuição

1. Faça um fork do repositório
2. Crie uma branch para sua feature: `git checkout -b feature/nova-secao`
3. Edite o JSON do workbook ou adicione novos arquivos em `workbooks/`
4. Abra um Pull Request com descrição clara das mudanças

---

## 📄 Licença

Este repositório é de uso livre para fins educacionais e empresariais internos.
