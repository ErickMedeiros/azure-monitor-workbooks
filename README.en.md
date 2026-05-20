# azure-monitor-workbooks

A Workbook that performs resource inventory to evaluate metrics — still under development.

🌐 **Leia em outros idiomas:** [Português](./README.md)

## 📌 Project purpose

This repository provides **Azure Monitor Workbooks** focused on **resource inventory** in Azure environments, allowing you to:

- Consolidate the view of provisioned resources across one or more subscriptions;
- Evaluate relevant **metrics** for usage, health and performance;
- Support **governance**, **FinOps** and **observability** activities;
- Serve as a baseline for customizations and improvements based on each environment's needs.

> ⚠️ This project is still under **active development**. Structures, queries (KQL/ARG) and visualizations may change.

## 📄 About the `workbook.json` file

The [`workbook.json`](./workbook.json) file contains the **serialized definition of the Azure Monitor Workbook** used in this project. It follows the standard Workbook export/import format (Gallery Template), including:

- Input parameters (subscriptions, resource groups, time ranges, etc.);
- Queries (Azure Resource Graph / Kusto) that collect the inventory and metrics;
- Visualizations (tables, charts and tiles) that organize the results.

### How to use

1. Open the Azure portal: **Azure Monitor → Workbooks → + New**.
2. Click the **</> (Advanced Editor)** icon.
3. Select the **Gallery Template (ARM)** type.
4. Copy the content of [`workbook.json`](./workbook.json) and paste it into the editor.
5. Click **Apply** and then **Save** to persist the Workbook in your subscription/resource group.

### Prerequisites

- Read permissions on the resources to be inventoried (e.g., `Reader`);
- Access to **Azure Monitor** and **Azure Resource Graph**.

## 📜 License

Distributed under the **MIT** License. See [LICENSE](./LICENSE) for more information.

## ✍️ Author

Project developed and maintained by **[Erick Medeiros](https://github.com/ErickMedeiros)**.

Contributions, suggestions and issues are welcome!
