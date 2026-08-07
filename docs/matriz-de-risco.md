## Matriz de risco

Para priorizar os esforços de mitigação, cada ameaça identificada na modelagem STRIDE foi classificada quanto à probabilidade de ocorrência e ao impacto caso se concretize. O nível de risco é obtido pelo cruzamento dessas duas dimensões (Baixo, Médio ou Alto).

| ID  | Ameaça | Probabilidade | Impacto | Risco |
|-----|--------|:---:|:---:|:---:|
| T01 | Acesso a conta de cliente/entregador via credenciais roubadas | Alta | Alto | **Alto** |
| T02 | Alteração indevida de valor, itens ou endereço do pedido | Média | Alto | **Alto** |
| T03 | Entregador ou cliente nega ter realizado/recebido a entrega | Média | Médio | **Médio** |
| T04 | Acesso indevido a dados pessoais e de pagamento de outros usuários | Média | Alto | **Alto** |
| T05 | Sobrecarga da API em horário de pico (DoS) | Baixa | Alto | **Médio** |
| T06 | Exploração de falha de autorização no painel admin | Baixa | Alto | **Médio** |

### Critérios utilizados

**Probabilidade**
- **Alta:** ataque simples de executar, não exige conhecimento técnico avançado (ex.: uso de credenciais vazadas/reutilizadas).
- **Média:** exige algum esforço técnico ou condição específica (ex.: manipular parâmetros da requisição, agir no momento certo da entrega).
- **Baixa:** exige recursos, coordenação ou conhecimento técnico maiores (ex.: ataque de negação de serviço em escala, exploração de falha específica de autorização).

**Impacto**
- **Alto:** prejuízo financeiro direto, violação de privacidade de dados sensíveis ou comprometimento de múltiplos usuários.
- **Médio:** prejuízo pontual, contornável por meio de contestação/reembolso, ou afeta um número limitado de usuários.
- **Baixo:** impacto operacional pequeno, sem prejuízo financeiro direto.

### Priorização

Os riscos classificados como **Alto** (T01, T02 e T04) devem ser tratados com prioridade, pois envolvem diretamente prejuízo financeiro e exposição de dados pessoais — coerente com o que já havia sido apontado nas considerações finais da Etapa 1. Os riscos **Médios** (T03, T05 e T06) também precisam de controles, mas podem ser implementados em uma segunda fase, já que exigem mais esforço do atacante ou têm impacto mais contido.