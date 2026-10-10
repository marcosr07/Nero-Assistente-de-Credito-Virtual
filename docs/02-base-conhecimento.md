# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `01_parametros_cliente.csv` | CSV | Parametros dos clientes com base na avaliação de crédito |
| `02_historico_movimentacoes.csv` | CSV | Confirmar se a renda está de acordo com as movimentações |
| `03_perfil_cliente.csv` | CSV | Adaptar a abordagem de acordo com o Perfil |
| `04_historico_atendimento.csv` | CSV | Analisar padrão de atendimentos anteriores |
| `05_catalogo_produtos_financeiros.csv` | CSV | Sugerir produtos e soluções financeiras |

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

Sim. Todos utilizam o campo cliente_id como identificador comum. Assim, o agente consegue relacionar os parâmetros cadastrais, as movimentações financeiras, o perfil e o histórico de atendimento do mesmo cliente.
Os exemplos incluem cinco clientes fictícios, com perfis variados de pessoa física e jurídica. As condições de elegibilidade são ilustrativas e não representam aprovações reais.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

O agente Nexo utiliza cinco arquivos CSV como fontes estruturadas de informação:

01_parametros_cliente.csv — dados cadastrais, score, renda, comprometimento financeiro, tempo de relacionamento, restrições e produtos disponíveis.

02_historico_movimentacoes.csv — entradas, saídas, saldos e comportamento financeiro do cliente.

03_perfil_cliente.csv — perfil financeiro, objetivos, necessidades de crédito e informações sobre interesse em investimentos.

04_historico_atendimento.csv — interações anteriores, produtos discutidos, respostas fornecidas e pendências.

05_catalogo_produtos_financeiros.csv — produtos disponíveis, requisitos de elegibilidade, público-alvo, garantias e condições comerciais documentadas.

No início de cada sessão, o sistema recebe automaticamente o identificador do cliente e recupera seus parâmetros cadastrais e financeiros.

As demais bases são consultadas conforme a solicitação do cliente. Por exemplo, uma pergunta sobre movimentações financeiras aciona a consulta ao histórico de movimentações, enquanto uma dúvida sobre empréstimos utiliza o catálogo de produtos e as regras de elegibilidade.

Os arquivos podem ser carregados por meio de Python com a biblioteca Pandas, armazenados em um banco de dados ou disponibilizados por uma API interna. Para um protótipo, os CSVs podem ser lidos no início da aplicação e mantidos em memória durante a execução.

Em ambiente de produção, os dados devem ser atualizados a partir de fontes autorizadas, com validação de integridade, controle de acesso, isolamento por cliente e registro da data da última atualização.

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

O Nexo utiliza uma abordagem híbrida, combinando instruções permanentes, dados contextuais e consultas dinâmicas.

1. System prompt (instruções permanentes)

O system prompt define a identidade do agente, o tom de comunicação, as regras de segurança, a política anti-alucinação e os critérios gerais de atendimento.

Ele estabelece que o Nexo deve utilizar somente dados e fontes autorizados, não inventar informações e diferenciar elegibilidade inicial de aprovação definitiva.

As regras críticas de concessão de crédito devem ser implementadas também em um motor de regras determinístico, em vez de depender exclusivamente da interpretação do modelo de linguagem.

2. Contexto da sessão

Os parâmetros do cliente são recuperados automaticamente e enviados como contexto da interação. Isso permite personalizar o atendimento sem solicitar novamente informações já disponíveis.

Somente os dados relevantes para a solicitação devem ser incluídos no contexto, minimizando a exposição de informações financeiras e pessoais.

3. Consulta dinâmica às bases

Quando necessário, o sistema consulta os CSVs para recuperar informações específicas. A consulta deve utilizar o identificador do cliente e retornar somente os registros pertinentes.

Exemplos:

“Quais créditos posso solicitar?” — consulta parâmetros, perfil e catálogo de produtos, além de aplicar as regras de elegibilidade.

“Quanto entrou na minha conta este mês?” — consulta o histórico de movimentações do período solicitado.

“Qual produto foi discutido no último atendimento?” — consulta o histórico de atendimento.

“Quais são os requisitos do financiamento de veículo?” — consulta o catálogo e a política de crédito vigente.

4. Validação antes da resposta

Os resultados são validados antes de serem apresentados ao cliente. O sistema verifica a disponibilidade dos dados, a aplicação dos critérios, a atualidade das informações e a existência de uma fonte autorizada.

Se faltarem dados, o agente informa a limitação. Se os critérios iniciais forem atendidos, apresenta o produto como elegível para análise, sem garantir aprovação.

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```
IDENTIDADE DO AGENTE
Nome: Nexo — Assistente Bancário Inteligente
Função: Atendimento bancário comercial e triagem de elegibilidade.
Tom: Formal, objetivo, preciso e conciso.

INSTRUÇÕES
- Utilizar somente informações fornecidas por fontes autorizadas.
- Não inventar taxas, limites, prazos ou condições comerciais.
- Não solicitar novamente dados já disponíveis e validados.
- Aplicar as regras de elegibilidade vigentes.
- Diferenciar elegibilidade, pré-aprovação e aprovação definitiva.
- Se houver informações insuficientes, declarar a limitação.
- Não apresentar uma aprovação sem confirmação oficial.

DADOS DO CLIENTE
Cliente ID: CLIENTE_001
Tipo de pessoa: PF
Score: 700
Relacionamento bancário: 6 meses
Cadastro completo: Sim
Situação da renda: Comprovada
Renda líquida mensal: R$ 5.000,00
Comprometimento da renda: 25%
Restrição impeditiva: Não identificada
Garantias disponíveis: Nenhuma informada

PERFIL FINANCEIRO
Perfil: Tomador potencial
Objetivo registrado: Organizar despesas
Necessidade identificada: Cartão de crédito e limite de conta
Interesse em investimentos: Não informado

HISTÓRICO DE MOVIMENTAÇÕES
Entradas e saídas: Consultar registros disponíveis.
Recorrência de recebimentos: Validar com base no período analisado.
Saldo atual: Não informado neste contexto.

HISTÓRICO DE ATENDIMENTO
Última interação: Consulta de elegibilidade.
Produtos discutidos: Cartão de crédito e cheque especial.
Pendência: Confirmar condições e capacidade no sistema oficial de crédito.

REGRAS DE ELEGIBILIDADE APLICÁVEIS
Cartão de crédito:
- Score de referência: a partir de 501.
- Relacionamento de referência: mínimo de 3 meses.
- Exige validação cadastral e análise de capacidade financeira.

Cheque especial:
- Score de referência: a partir de 601.
- Relacionamento de referência: mínimo de 3 meses.
- Exige avaliação da movimentação, renda e endividamento.

Empréstimo pessoal:
- Score de referência: a partir de 651.
- Relacionamento de referência: mínimo de 6 meses.
- Exige validação da renda, capacidade de pagamento e histórico.

RESULTADO DA TRIAGEM
O cliente atende às referências iniciais de score e
relacionamento para cartão, cheque especial e empréstimo pessoal.
A elegibilidade definitiva depende da validação dos demais
requisitos de cada produto.

TAREFA
Responder à solicitação do cliente usando os dados acima.
Apresentar somente informações fundamentadas.
Ser curto, formal e objetivo.
Não declarar aprovação ou limite disponível sem confirmação oficial.
...
```

