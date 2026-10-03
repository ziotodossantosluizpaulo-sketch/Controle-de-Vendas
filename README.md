<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Vendas e Pagamentos Parciais</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 min-h-screen text-slate-800">

    <div class="max-w-7xl mx-auto px-4 py-8">
        <!-- Cabeçalho -->
        <header class="mb-8">
            <h1 class="text-3xl font-bold text-slate-900">Controle de Vendas</h1>
            <p class="text-slate-600">Gerenciamento de clientes, datas, valores e pagamentos parciais.</p>
        </header>

        <!-- Painel / Dashboard -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-8">
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <p class="text-sm font-medium text-slate-500">Total de Vendas</p>
                <h3 id="cardTotalVendas" class="text-2xl font-bold text-slate-900 mt-1">R$ 0,00</h3>
            </div>
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <p class="text-sm font-medium text-slate-500">Total Recebido</p>
                <h3 id="cardTotalRecebido" class="text-2xl font-bold text-emerald-600 mt-1">R$ 0,00</h3>
            </div>
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <p class="text-sm font-medium text-slate-500">Saldo a Receber</p>
                <h3 id="cardSaldoReceber" class="text-2xl font-bold text-amber-600 mt-1">R$ 0,00</h3>
            </div>
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <p class="text-sm font-medium text-slate-500">Pendentes / Parciais</p>
                <h3 id="cardQtdPendentes" class="text-2xl font-bold text-blue-600 mt-1">0</h3>
            </div>
        </div>

        <!-- Formulário e Tabela -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <!-- Formulário de Nova Venda -->
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200 h-fit">
                <h2 class="text-lg font-semibold text-slate-900 mb-4">Cadastrar Nova Venda</h2>
                <form id="formVenda" class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Nome do Cliente</label>
                        <input type="text" id="cliente" required class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none" placeholder="Ex: Maria Silva">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Data da Venda</label>
                        <input type="date" id="dataVenda" required class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Valor Total (R$)</label>
                        <input type="number" step="0.01" id="valorTotal" required class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none" placeholder="0.00">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Valor Inicial Pago (R$)</label>
                        <input type="number" step="0.01" id="valorPago" value="0" class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none" placeholder="0.00">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Data do Pagamento Parcial</label>
                        <input type="date" id="dataPagamento" class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-lg transition duration-200">
                        Salvar Venda
                    </button>
                </form>
            </div>

            <!-- Lista de Vendas -->
            <div class="lg:col-span-2 bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <div class="flex flex-col sm:flex-row justify-between items-center mb-6 gap-4">
                    <h2 class="text-lg font-semibold text-slate-900">Histórico de Vendas</h2>
                    <input type="text" id="buscaCliente" placeholder="Pesquisar cliente..." class="px-3 py-1.5 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none w-full sm:w-64">
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-slate-200 text-xs font-semibold text-slate-500 uppercase">
                                <th class="pb-3 px-2">Cliente</th>
                                <th class="pb-3 px-2">Data Venda</th>
                                <th class="pb-3 px-2">Total</th>
                                <th class="pb-3 px-2">Pago</th>
                                <th class="pb-3 px-2">Restante</th>
                                <th class="pb-3 px-2">Status</th>
                                <th class="pb-3 px-2 text-right">Ações</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaVendas" class="divide-y divide-slate-100 text-sm">
                            <!-- Os dados entram aqui dinamicamente -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>
    </div>

    <!-- Script de Funcionamento -->
    <script>
        let vendas = JSON.parse(localStorage.getItem('vendas_controle')) || [];

        document.getElementById('dataVenda').valueAsDate = new Date();
        document.getElementById('dataPagamento').valueAsDate = new Date();

        const form = document.getElementById('formVenda');
        const tabela = document.getElementById('tabelaVendas');
        const busca = document.getElementById('buscaCliente');

        form.addEventListener('submit', (e) => {
            e.preventDefault();
            
            const novaVenda = {
                id: Date.now(),
                cliente: document.getElementById('cliente').value,
                dataVenda: document.getElementById('dataVenda').value,
                valorTotal: parseFloat(document.getElementById('valorTotal').value),
                valorPago: parseFloat(document.getElementById('valorPago').value) || 0,
                dataPagamento: document.getElementById('dataPagamento').value || '-'
            };

            vendas.push(novaVenda);
            salvarERenderizar();
            form.reset();
            document.getElementById('dataVenda').valueAsDate = new Date();
            document.getElementById('valorPago').value = 0;
        });

        function adicionarPagamentoParcial(id) {
            const venda = vendas.find(v => v.id === id);
            if (!venda) return;

            const saldoRestante = venda.valorTotal - venda.valorPago;
            const valorExtra = prompt(`Saldo restante: R$ ${saldoRestante.toFixed(2)}\nDigite o valor do novo pagamento parcial:`);
            
            if (valorExtra !== null && !isNaN(valorExtra) && valorExtra.trim() !== '') {
                const numExtra = parseFloat(valorExtra);
                if (numExtra > 0) {
                    venda.valorPago += numExtra;
                    if (venda.valorPago > venda.valorTotal) venda.valorPago = venda.valorTotal;
                    venda.dataPagamento = new Date().toISOString().split('T')[0];
                    salvarERenderizar();
                }
            }
        }

        function excluirVenda(id) {
            if (confirm('Deseja realmente excluir esta venda?')) {
                vendas = vendas.filter(v => v.id === id);
                salvarERenderizar();
            }
        }

        function salvarERenderizar() {
            localStorage.setItem('vendas_controle', JSON.stringify(vendas));
            renderizarDashboard();
            renderizarTabela();
        }

        function renderizarDashboard() {
            let totalVendas = 0;
            let totalRecebido = 0;
            let pendentesCount = 0;

            vendas.forEach(v => {
                totalVendas += v.valorTotal;
                totalRecebido += v.valorPago;
                if (v.valorPago < v.valorTotal) pendentesCount++;
            });

            document.getElementById('cardTotalVendas').innerText = `R$ ${totalVendas.toFixed(2)}`;
            document.getElementById('cardTotalRecebido').innerText = `R$ ${totalRecebido.toFixed(2)}`;
            document.getElementById('cardSaldoReceber').innerText = `R$ ${(totalVendas - totalRecebido).toFixed(2)}`;
            document.getElementById('cardQtdPendentes').innerText = pendentesCount;
        }

        function renderizarTabela() {
            tabela.innerHTML = '';
            const termoBusca = busca.value.toLowerCase();

            const filtradas = vendas.filter(v => v.cliente.toLowerCase().includes(termoBusca));

            if (filtradas.length === 0) {
                tabela.innerHTML = `<tr><td colspan="7" class="py-4 text-center text-slate-400">Nenhuma venda encontrada.</td></tr>`;
                return;
            }

            filtradas.forEach(v => {
                const restante = v.valorTotal - v.valorPago;
                let statusBadge = '';

                if (v.valorPago >= v.valorTotal) {
                    statusBadge = `<span class="px-2.5 py-1 text-xs font-semibold bg-emerald-100 text-emerald-800 rounded-full">Pago</span>`;
                } else if (v.valorPago > 0) {
                    statusBadge = `<span class="px-2.5 py-1 text-xs font-semibold bg-amber-100 text-amber-800 rounded-full">Parcial</span>`;
                } else {
                    statusBadge = `<span class="px-2.5 py-1 text-xs font-semibold bg-rose-100 text-rose-800 rounded-full">Pendente</span>`;
                }

                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="py-3 px-2 font-medium text-slate-900">${v.cliente}</td>
                    <td class="py-3 px-2 text-slate-600">${v.dataVenda.split('-').reverse().join('/')}</td>
                    <td class="py-3 px-2 text-slate-900 font-semibold">R$ ${v.valorTotal.toFixed(2)}</td>
                    <td class="py-3 px-2 text-emerald-600 font-medium">R$ ${v.valorPago.toFixed(2)}</td>
                    <td class="py-3 px-2 text-amber-600 font-medium">R$ ${restante.toFixed(2)}</td>
                    <td class="py-3 px-2">${statusBadge}</td>
                    <td class="py-3 px-2 text-right space-x-2">
                        <button onclick="adicionarPagamentoParcial(${v.id})" class="text-blue-600 hover:text-blue-800 text-xs font-medium bg-blue-50 px-2 py-1 rounded"> + Pgt </button>
                        <button onclick="excluirVenda(${v.id})" class="text-rose-600 hover:text-rose-800 text-xs font-medium bg-rose-50 px-2 py-1 rounded">Excluir</button>
                    </td>
                `;
                tabela.appendChild(tr);
            });
        }

        busca.addEventListener('input', renderizarTabela);

        // Inicializa a tela ao abrir
        salvarERenderizar();
    </script>
</body>
</html>
