<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cardápio - Cantina Gigio</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; padding: 0; background: #fdf7f0; }
    header { background: #444; color: #fff; padding: 1em; text-align: center; }
    .container { padding: 1em; }
    .item { border-bottom: 1px solid #ccc; padding: 1em 0; }
    .item h3 { margin: 0.2em 0; }
    .item button { margin-top: 0.5em; background: #4caf50; color: #fff; border: none; padding: 0.5em 1em; border-radius: 5px; cursor: pointer; }
    .item button:hover { background: #45a049; }
    #carrinho { margin-top: 2em; padding: 1em; background: #fff3cd; border: 1px solid #ffeeba; border-radius: 5px; }
    textarea { width: 100%; margin-top: 1em; }
    select, input[type="text"] { width: 100%; margin-top: 0.5em; padding: 0.5em; }
    #finalizar { background: #007bff; color: #fff; border: none; padding: 0.75em 1.5em; margin-top: 1em; border-radius: 5px; cursor: pointer; }
    #finalizar:hover { background: #0056b3; }
  </style>
</head>
<body>
  <header>
    <h1>Cantina Gigio - Cardápio Online</h1>
  </header>
  <div class="container">
    <div id="cardapio">Carregando cardápio...</div>
    <div id="carrinho">
      <h2>🛒 Seu pedido</h2>
      <ul id="itens"></ul>
      <label for="entrega">Forma de entrega:</label>
      <select id="entrega">
        <option value="">Selecione</option>
        <option>Delivery</option>
        <option>Consumo no local</option>
        <option>Mesa</option>
        <option>Balcão</option>
      </select>
      <label for="pagamento">Forma de pagamento:</label>
      <select id="pagamento">
        <option value="">Selecione</option>
        <option>PIX</option>
        <option>Dinheiro</option>
        <option>Cartão</option>
      </select>
      <textarea id="observacoes" placeholder="Observações adicionais..."></textarea>
      <button id="finalizar">Finalizar e Enviar via WhatsApp</button>
    </div>
  </div>
  <script>
    const cardapioContainer = document.getElementById('cardapio');
    const itensCarrinho = document.getElementById('itens');
    const carrinho = [];

    async function carregarCardapio() {
      const url = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTODbpdpKUUUzRip0QWPKSEEgpan_vVUXzdIxVO5lvc5Bybe__Oe9psLfV82a9A8N1M6V0s7poDcKbB/pub?output=csv';
      const resposta = await fetch(url);
      const texto = await resposta.text();
      const linhas = texto.split('\n');
      const cabecalhos = linhas[0].split(',');
      const dados = linhas.slice(1);

      cardapioContainer.innerHTML = '';
      dados.forEach(linha => {
        const colunas = linha.split(',');
        const item = {
          nome: colunas[0],
          categoria: colunas[1],
          descricao: colunas[2],
          disponivel: colunas[3],
          preco: parseFloat(colunas[4]),
          observacao: colunas[5],
          foto: colunas[6]
        };

        if (item.disponivel.toLowerCase() === 'sim') {
          const div = document.createElement('div');
          div.className = 'item';
          div.innerHTML = `<h3>${item.nome} - R$ ${item.preco.toFixed(2)}</h3><p>${item.descricao}</p><small>${item.observacao}</small><br><button onclick='adicionarItem(${JSON.stringify(item)})'>Adicionar</button>`;
          cardapioContainer.appendChild(div);
        }
      });
    }

    function adicionarItem(item) {
      carrinho.push(item);
      atualizarCarrinho();
    }

    function atualizarCarrinho() {
      itensCarrinho.innerHTML = '';
      carrinho.forEach((item, i) => {
        const li = document.createElement('li');
        li.textContent = `${item.nome} - R$ ${item.preco.toFixed(2)}`;
        itensCarrinho.appendChild(li);
      });
    }

    document.getElementById('finalizar').onclick = () => {
      const entrega = document.getElementById('entrega').value;
      const pagamento = document.getElementById('pagamento').value;
      const observacoes = document.getElementById('observacoes').value;
      let mensagem = '*Pedido Cantina Gigio*%0A';
      carrinho.forEach(item => {
        mensagem += `- ${item.nome} (R$ ${item.preco.toFixed(2)})%0A`;
      });
      mensagem += `%0A*Entrega:* ${entrega}%0A*Pagamento:* ${pagamento}%0A*Observações:* ${observacoes}`;

      const url = `https://wa.me/555399966969?text=${mensagem}`;
      window.open(url, '_blank');
    }

    carregarCardapio();
  </script>
</body>
</html>
