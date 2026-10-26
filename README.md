### Assistente Financeiro de Open Finance via Protocolo MCP

Web aplicativo conversacional projetado para atuar como um assistente de finanças pessoais integrado nativamente a fontes externas de dados bancários reais e simulados através do **Model Context Protocol (MCP)**, utilizando as infraestruturas da Plaid (Ambiente Sandbox transacional) e Pluggy (Documentação Viva). 

O projeto elimina interfaces complexas de Internet Banking, permitindo ao correntista extrair saldos, faturas e tabelas de consumo diretamente através de comandos simples em linguagem natural. 

### Perguntas de Negócio e Jornada do Usuário

O assistente responde de forma transparente a duas dores cruciais do cliente final no dia a dia: 

**"Qual é o meu saldo consolidado?"** 

  * *O que o sistema faz:* Aciona a ferramenta vinculada ao endpoint de balanço da Plaid para consolidar em uma tabela limpa os saldos de Conta Corrente, Poupança e Cartão.
**"Quais são os meus padrões de gastos?"** 

  * *O que o sistema faz:* Varre a API histórica de transações do cliente, agrupa os lançamentos por categorias de mercado e retorna uma matriz analítica de consumo.

### Fundamentos de Arquitetura e Engenharia de Dados

### O Fluxo da Resposta (Da Pergunta ao Endpoint)

Quando o usuário envia a mensagem "qual é o meu saldo?", o fluxo de TI executa os seguintes passos lógicos em milissegundos: 

O modelo de IA atua como **Orquestrador**, lê o texto e identifica a intenção do usuário através de processamento de linguagem natural.
A IA dispara uma chamada de ferramenta (*Tool Call*) via protocolo **MCP** para o servidor de integração.
O servidor traduz essa instrução na requisição HTTP padrão exigida pela API (ex: POST /accounts/balance/get), recolhendo o JSON bruto estruturado com os saldos numéricos.
O payload retorna para a IA, que interpreta os dados matemáticos e traduz a resposta técnica em um texto explicativo e amigável em português para o cliente final.

### Regras de Negócio e Tratamento de Sinais

* **Tratamento de Sinais da Plaid:** Nativamente em APIs financeiras internacionais, as saídas de caixa (gastos) são tratadas como números positivos. O motor de dados do aplicativo inverteu esse sinal, aplicando a máscara de subtração (- R$) na tela para se alinhar ao padrão de leitura brasileiro.
* **A Linha de Crédito como Passivo:** O saldo do cartão de crédito aparece como um valor positivo na API porque o protocolo mapeia a conta sob a categoria de *Passivo/Dívida* (liability). O número representa o balanço real de obrigações financeiras acumuladas pelo titular que deverão ser quitadas no vencimento da fatura.

### Parametrização dos Servidores MCP

Para reprodução em ambiente de desenvolvimento, o arquivo de configuração de protocolo MCP (mcp_config.json) deve ser parametrizado com a seguinte estrutura de servidores: 

json

{
  "mcpServers": {
    "pluggy-docs": {
      "command": "curl",
      "args": ["-s", "https://mcp.pluggy.ai/mcp"],
      "transport": "http"
    },
    "mcp-server-plaid": {
      "command": "uvx",
      "args": ["mcp-server-plaid"],
      "env": {
        "PLAID_CLIENT_ID": "SEU_CLIENT_ID_AQUI",
        "PLAID_SECRET": "SEU_SECRET_AQUI",
        "PLAID_ENV": "sandbox"
      }
    }
  }
}

### Como Executar o Web App

Baixe os arquivos deste repositório na sua máquina.
Dê um duplo clique diretamente no arquivo index.html para executá-lo instantaneamente no seu navegador de internet.
Digite **"saldo"** ou **"gastos"** no campo do chat para testar a inteligência de processamento de dados e as respostas estruturadas via MCP.

* **URL do Aplicativo:** https://github.io

