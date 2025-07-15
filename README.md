<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contas a Receber - Planilha</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f5f5f5;
        }
        .container {
            background-color: white;
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #2c3e50;
            text-align: center;
            margin-bottom: 30px;
        }
        .summary {
            display: flex;
            justify-content: space-around;
            margin-bottom: 30px;
            background-color: #ecf0f1;
            padding: 15px;
            border-radius: 5px;
        }
        .summary-item {
            text-align: center;
        }
        .summary-value {
            font-size: 24px;
            font-weight: bold;
            color: #27ae60;
        }
        .summary-label {
            color: #7f8c8d;
            font-size: 14px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 12px;
            text-align: left;
        }
        th {
            background-color: #3498db;
            color: white;
            font-weight: bold;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        tr:hover {
            background-color: #e8f4f8;
        }
        .value {
            text-align: right;
            font-family: monospace;
        }
        .overdue {
            background-color: #ffebee !important;
            color: #c62828;
        }
        .current {
            background-color: #e8f5e8 !important;
            color: #2e7d32;
        }
        .status {
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 12px;
            font-weight: bold;
            text-align: center;
        }
        .filters {
            margin-bottom: 20px;
        }
        select, input {
            margin: 5px;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        .total-row {
            background-color: #2c3e50 !important;
            color: white;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📊 Contas a Receber - Controle de Recebíveis</h1>
        
        <div class="summary">
            <div class="summary-item">
                <div class="summary-value" id="totalReceivables">R$ 0,00</div>
                <div class="summary-label">Total a Receber</div>
            </div>
            <div class="summary-item">
                <div class="summary-value" id="overdueCount">0</div>
                <div class="summary-label">Contas Vencidas</div>
            </div>
            <div class="summary-item">
                <div class="summary-value" id="currentCount">0</div>
                <div class="summary-label">Contas Correntes</div>
            </div>
        </div>

        <div class="filters">
            <label>Filtrar por cliente:</label>
            <select id="clientFilter">
                <option value="">Todos os clientes</option>
            </select>
            
            <label>Filtrar por status:</label>
            <select id="statusFilter">
                <option value="">Todos</option>
                <option value="current">Correntes</option>
                <option value="overdue">Vencidas</option>
            </select>
            
            <label>Buscar:</label>
            <input type="text" id="searchInput" placeholder="Digite para buscar...">
        </div>

        <table id="receivablesTable">
            <thead>
                <tr>
                    <th>Data Vencimento</th>
                    <th>Cliente</th>
                    <th>Empresa</th>
                    <th>Centro de Custo</th>
                    <th>Valor Original</th>
                    <th>Valor Corrigido</th>
                    <th>Valor Total</th>
                    <th>Status</th>
                    <th>Dias p/ Vencimento</th>
                </tr>
            </thead>
            <tbody id="receivablesBody">
                <!-- Dados serão inseridos aqui -->
            </tbody>
            <tfoot>
                <tr class="total-row">
                    <td colspan="6"><strong>TOTAL GERAL</strong></td>
                    <td class="value"><strong id="grandTotal">R$ 0,00</strong></td>
                    <td colspan="2"></td>
                </tr>
            </tfoot>
        </table>
    </div>

    <script>
        // Dados extraídos do relatório
        const receivablesData = [
            {
                date: '2025-07-15',
                client: 'HUMBERTO GEOVANNI DALMAZ',
                company: 'JARDINS CAIOBA SPE LTDA - OBRA',
                costCenter: '204',
                originalValue: 4000.00,
                correctedValue: 4000.00,
                totalValue: 4000.00,
                indexer: 'INCC-DI'
            },
            {
                date: '2025-07-25',
                client: 'MSFS ADMINISTRACAO DE BENS LTD',
                company: 'MAISON LUMIERE SPE LTDA',
                costCenter: '61',
                originalValue: 71200.00,
                correctedValue: 76731.01,
                totalValue: 76731.01,
                indexer: 'M%'
            },
            {
                date: '2025-07-30',
                client: 'HELCIO BELACHE FERREIRA',
                company: 'CYANO GUARATUBA - OBRA',
                costCenter: '302',
                originalValue: 3409.09,
                correctedValue: 3601.04,
                totalValue: 3601.04,
                indexer: 'INCC-M'
            },
            {
                date: '2025-07-30',
                client: 'HELCIO BELACHE FERREIRA',
                company: 'JARDINS CAIOBA SPE LTDA - OBRA',
                costCenter: '601',
                originalValue: 12708.93,
                correctedValue: 13999.04,
                totalValue: 13999.04,
                indexer: 'INCC-M'
            },
            {
                date: '2025-08-01',
                client: 'FERNANDA BETTINARDI',
                company: 'MAISON LUMIERE SPE LTDA',
                costCenter: '41',
                originalValue: 12500.00,
                correctedValue: 13535.71,
                totalValue: 13535.71,
                indexer: 'M%'
            },
            {
                date: '2025-08-05',
                client: 'FABIANA MASSAF IAMAGUIHARA',
                company: 'CYANO GUARATUBA - OBRA',
                costCenter: '303',
                originalValue: 10000.00,
                correctedValue: 10287.66,
                totalValue: 10287.66,
                indexer: 'CUB R 8-N'
            },
            {
                date: '2025-08-10',
                client: 'PINHAIS COMERCIO DE ALIMENTOS',
                company: 'CYANO GUARATUBA - RECEBIMENTOS',
                costCenter: '601',
                originalValue: 25637.03,
                correctedValue: 25637.03,
                totalValue: 25637.03,
                indexer: 'M%'
            },
            {
                date: '2025-08-15',
                client: 'HUMBERTO GEOVANNI DALMAZ',
                company: 'JARDINS CAIOBA SPE LTDA - OBRA',
                costCenter: '204',
                originalValue: 4000.00,
                correctedValue: 4000.00,
                totalValue: 4000.00,
                indexer: 'INCC-DI'
            },
            {
                date: '2025-08-25',
                client: 'MSFS ADMINISTRACAO DE BENS LTD',
                company: 'MAISON LUMIERE SPE LTDA',
                costCenter: '61',
                originalValue: 71200.00,
                correctedValue: 76731.01,
                totalValue: 76731.01,
                indexer: 'M%'
            },
            {
                date: '2025-08-30',
                client: 'HELCIO BELACHE FERREIRA',
                company: 'CYANO GUARATUBA - OBRA',
                costCenter: '302',
                originalValue: 3409.09,
                correctedValue: 3601.04,
                totalValue: 3601.04,
                indexer: 'INCC-M'
            },
            {
                date: '2025-08-30',
                client: 'HELCIO BELACHE FERREIRA',
                company: 'JARDINS CAIOBA SPE LTDA - OBRA',
                costCenter: '601',
                originalValue: 12708.93,
                correctedValue: 13999.04,
                totalValue: 13999.04,
                indexer: 'INCC-M'
            }
        ];

        function formatCurrency(value) {
            return new Intl.NumberFormat('pt-BR', {
                style: 'currency',
                currency: 'BRL'
            }).format(value);
        }

        function calculateDaysToExpire(dateStr) {
            const today = new Date();
            const expireDate = new Date(dateStr);
            const diffTime = expireDate - today;
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
            return diffDays;
        }

        function getStatus(days) {
            if (days < 0) return 'overdue';
            return 'current';
        }

        function getStatusText(days) {
            if (days < 0) return 'Vencida';
            if (days === 0) return 'Vence Hoje';
            return 'Corrente';
        }

        function populateTable(data = receivablesData) {
            const tbody = document.getElementById('receivablesBody');
            tbody.innerHTML = '';
            
            let totalValue = 0;
            let overdueCount = 0;
            let currentCount = 0;
            
            data.forEach(item => {
                const row = tbody.insertRow();
                const days = calculateDaysToExpire(item.date);
                const status = getStatus(days);
                
                if (status === 'overdue') {
                    overdueCount++;
                    row.classList.add('overdue');
                } else {
                    currentCount++;
                    row.classList.add('current');
                }
                
                totalValue += item.totalValue;
                
                row.innerHTML = `
                    <td>${new Date(item.date).toLocaleDateString('pt-BR')}</td>
                    <td>${item.client}</td>
                    <td>${item.company}</td>
                    <td>${item.costCenter}</td>
                    <td class="value">${formatCurrency(item.originalValue)}</td>
                    <td class="value">${formatCurrency(item.correctedValue)}</td>
                    <td class="value">${formatCurrency(item.totalValue)}</td>
                    <td><span class="status ${status}">${getStatusText(days)}</span></td>
                    <td class="value">${days} dias</td>
                `;
            });
            
            // Atualizar resumo
            document.getElementById('totalReceivables').textContent = formatCurrency(totalValue);
            document.getElementById('overdueCount').textContent = overdueCount;
            document.getElementById('currentCount').textContent = currentCount;
            document.getElementById('grandTotal').textContent = formatCurrency(totalValue);
        }

        function populateFilters() {
            const clientFilter = document.getElementById('clientFilter');
            const uniqueClients = [...new Set(receivablesData.map(item => item.client))];
            
            uniqueClients.forEach(client => {
                const option = document.createElement('option');
                option.value = client;
                option.textContent = client;
                clientFilter.appendChild(option);
            });
        }

        function filterData() {
            const clientFilter = document.getElementById('clientFilter').value;
            const statusFilter = document.getElementById('statusFilter').value;
            const searchInput = document.getElementById('searchInput').value.toLowerCase();
            
            let filteredData = receivablesData;
            
            if (clientFilter) {
                filteredData = filteredData.filter(item => item.client === clientFilter);
            }
            
            if (statusFilter) {
                filteredData = filteredData.filter(item => {
                    const days = calculateDaysToExpire(item.date);
                    const status = getStatus(days);
                    return status === statusFilter;
                });
            }
            
            if (searchInput) {
                filteredData = filteredData.filter(item => 
                    item.client.toLowerCase().includes(searchInput) ||
                    item.company.toLowerCase().includes(searchInput) ||
                    item.costCenter.includes(searchInput)
                );
            }
            
            populateTable(filteredData);
        }

        // Event listeners
        document.getElementById('clientFilter').addEventListener('change', filterData);
        document.getElementById('statusFilter').addEventListener('change', filterData);
        document.getElementById('searchInput').addEventListener('input', filterData);

        // Inicializar
        populateTable();
        populateFilters();
    </script>
</body>
</html>
